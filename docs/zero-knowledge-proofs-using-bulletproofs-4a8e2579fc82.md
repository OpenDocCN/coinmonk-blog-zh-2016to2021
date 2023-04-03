# 使用 bullet 证明的零知识证明

> 原文：<https://medium.com/coinmonks/zero-knowledge-proofs-using-bulletproofs-4a8e2579fc82?source=collection_archive---------0----------------------->

![](img/ad4df10f6cad8c88d6c26098469992b0.png)

Photo by [Jeremy Perkins](https://unsplash.com/photos/1XgFFEG_RGA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/zero-knowledge?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

这里我将展示如何使用[*bullet proof 实现*](https://github.com/dalek-cryptography/bulletproofs)*[*dalek-cryptography*](https://github.com/dalek-cryptography)*创建各种零知识证明。这些例子将是:I)证明已知给定数目的因子，而不暴露这些因子，ii)范围证明，即证明你知道值 x，使得 a ≤ x ≤ b，而不暴露 x，iii)证明你拥有的值非零，而不暴露它(不使用上述范围证明)，iv)证明该值不等于给定值，而不暴露你自己的值，v)集合成员，即给定集合 S， 证明你知道集合中包含的一个元素而不暴露该元素，vi)类似地，设置非成员资格而不暴露缺失元素的值。 这些例子只需很少的努力就可以修改，这样它们就可以用在 libsnark 或 bellman 这样的 ZK-SNARK 实现中。要直接浏览代码，请点击* *进入* [*。*](https://github.com/lovesh/bulletproofs/blob/range-proof/tests)*

## *这篇文章中的代码已经被重构，更新了更多的例子，并转移到了一个新的[repo](https://github.com/lovesh/bulletproofs-r1cs-gadgets)。*

# *概观*

*我假设你已经熟悉利用算术电路来证明任意陈述。它已经在几篇论文中被描述过，但是本帖所讨论的库是建立在论文“[离散对数设置中算术电路的有效零知识论证](https://eprint.iacr.org/2016/263)”之上的。通过[的 Bulletproofs 论文](https://eprint.iacr.org/2017/1066.pdf)和 dalek-cryptography 出版了他们的[实现](https://github.com/dalek-cryptography/bulletproofs)，该技术变得更加有效。其思想是将语句表示为算术电路，即一组方程，其中允许的运算符是加法、减法和乘法，并将这些方程转换为秩 1 约束系统(R1CS)。约束系统是一组变量的算术约束的集合。要了解更多关于这个过程和 R1CS 的信息，请看这篇[文章](/@VitalikButerin/quadratic-arithmetic-programs-from-zero-to-hero-f6d558cea649)。最近，利用 r1c 的验证系统越来越受欢迎，ZK-斯纳克是最受欢迎的一种。ZK-斯纳克法的缺点是存在可信设置，即协议参数的一次性生成，这涉及到可以用来破坏协议保证的一些秘密的知识或学习。这是一个挑战，因为现在的设置必须以这样的方式完成，这样的秘密不能被(完全)学习，Zcash 使用多方计算来完成它。另一个挑战是必须对每条电路进行可信设置，例如，如果你想证明对 2 个因素的了解，那么你要进行可信设置。现在，如果您必须证明了解 3 个因素，您必须再次进行可信设置，因为约束条件已经改变。用 Bulletproofs 构建的 R1CS 系统没有可信的设置，因此避免了上述两个问题。*

*该验证系统的最高理念是
1。证明者承诺一个(多个)值，他想要证明对
2 的了解。证明者通过对承诺值和任何附加的公共值施加约束来生成证明。这些约束可能需要证明者提交一些额外的变量。
3。证明者向验证者发送他在步骤 1 和步骤 2 中做出的所有承诺，以及来自步骤 2 的证明。
4。验证者现在通过对承诺加上任何公共值实施相同的约束来验证证明。*

# *Bulletproofs API*

*让我们在一个示例的上下文中探索 Bulletproofs API。假设证明者想要证明一个公众号`r`的因子的知识，证明者知道因子`p`和`q`，但是验证者不知道。因此，证明者将为陈述`p * q = r`创建证明，其中`r`被证明者和验证者都知道，但是`p`和`q`仅被证明者知道。*

*1.创建一些将被证明者和验证者使用的生成器*

```
*let pc_gens = PedersenGens::*default*();
let bp_gens = BulletproofGens::*new*(128, 1);*
```

*上面的代码创建了 2 组生成器，`pc_gens`是一对 2 生成器，`bp_gens`是一对 2 向量(列表)生成器。自变量 128 表示每个向量将有 128 个生成器。所以在`bp_gens`中总共创建了 2*128=256 个生成器。`pc`和`bp`分别代表 Pedersen 和 Bulletproofs。*

*2.实例化一个证明者*

```
*let mut prover_transcript = Transcript::*new*(b"Factors");
let mut prover = Prover::*new*(&bp_gens, &pc_gens, &mut prover_transcript);*
```

*`prover_transcript`是证明者的抄本。副本是证明者和验证者之间交换的消息的记录，如证明者发送给验证者的承诺或验证者发送给证明者的挑战。由于这种 Bulletproofs 实现是非交互式的，挑战是由 Fiat-Shamir 启发式算法产生的，即散列抄本的当前状态。`Factors`是转录本的标签，当方案由子方案组成时，用于区分不同的转录本或子转录本。*

*3.证明者提交变量*

```
*let x1 = Scalar::*random*(&mut rng);
let (com_p, var_p) = prover.commit(p.into(), x1);
let x2 = Scalar::*random*(&mut rng);
let (com_q, var_q) = prover.commit(q.into(), x2);*
```

*在上面的例子中，证明者使用随机性`x1`和`pc_gens`的 2 个生成器`g`和`h`来提交因子`p`。这是彼得森的承诺，因此称为`com_p = gᵖhˣ¹`。同样`com_q`是对`q`与`x2`的承诺。除了创建承诺，`commit`方法还为约束系统创建相应的变量，`var_p`和`var_q`分别是`p`和`q`的变量。此外，在`commit`期间，2 项承诺被添加到记录中。*

*4.证明者约束变量*

```
*let (_, _, o) =  prover.multiply(var_p.into(), var_q.into());*
```

*通过`multiply`函数，证明者指定变量`var_p`和`var_q`要相乘，结果被捕获到变量`o`中。此外，该功能将导致分配对应于`var_p`、`var_q`和`o`的变量。另外`multiply`评估输入变量，并约束它们等于相应的分配变量。*

```
*let r_lc: LinearCombination = vec![(Variable::*One*(),      r.into())].iter().collect();
prover.constrain(o -  r_lc);*
```

*现在证明者想要强制执行`p`和`q`的乘积等于`r`。由于`p`和`q`的变量乘积，即`var_p`和`var_q`在变量`o`中被捕获，所以证明者可以为`r`分配一个变量，然后确保 *r 变量*和`o`的减法为 0。证明者通过创建线性约束来创建这个 *r 变量*，即一个变量乘以一个标量。元组`(Variable::*One*(), r.into())`表示值等于`r`的线性约束，即将值为 1 的变量(`Variable::*One*()`)乘以标量`r`。如果证明者想要创建一个值为 p+q+r 的变量，他可以通过`vec![(Variable::*One*(), (p+q+r).into())]`或`vec![(Variable::*One*(), p.into()), (Variable::*One*(), q.into()), (Variable::*One*(), r.into())]`创建向量的相加项。
由于线性组合可以彼此相加或相减以给出另一个线性组合，所以证明者确保线性组合`o — lc`为 0，这通过调用`constrain`来完成。方法`constrain`确保传递给它的线性组合等于 0。*

*5.证明者创造证据*

```
*let proof = prover.prove().unwrap();*
```

*6.实例化验证器*

```
*let mut verifier_transcript = Transcript::*new*(b"Factors");
let mut verifier = Verifier::*new*(&bp_gens, &pc_gens, &mut verifier_transcript);*
```

*在这里，验证器被实例化，并创建自己的副本。请注意，抄本的名称与证明者的名称相同。这很重要，因为名字是抄本内容的一部分，而证明者和验证者之间的挑战是抄本内容的散列。不同的名称将导致不同的抄本内容，因此对证明者和验证者的挑战也不同，这将导致证明验证失败。*

*7.使用承诺的验证者。*

```
*let var_p = verifier.commit(commitments.0);
let var_q = verifier.commit(commitments.1);*
```

*验证者在抄本中记录由证明者发送的对 p 和 q 的承诺，并为这些承诺创建类似于证明者的变量。区别在于证明者可以访问被提交的值和随机性，而验证者则不能。*

*8.验证器约束变量*

```
*let (_, _, o) =  verifier.multiply(var_p.into(), var_q.into());
let r_lc: LinearCombination = vec![(Variable::*One*(), r.into())].iter().collect();
verifier.constrain(o -  r_lc);*
```

*类似于证明者，验证者也约束对应于承诺的变量。请注意，约束与证明者的约束完全相同。*

*9.最后，验证者验证证明。*

```
*verifier.verify(&proof)*
```

*这个例子缺少一个约束来确保`p`和`q`都不能为 1(除非`r`为 1)，这可以通过使用范围证明(expenve)或证明不等于 1 来实现，如下例所示。完整的例子是[这里的](https://github.com/lovesh/bulletproofs/blob/e477511a20bdb8de8f4fa82cb789ba71cc66afd8/tests/basic_r1cs.rs#L17)。*

# *更多示例*

***量程证明** 为了证明一个承诺值`x`满足 *min ≤ x ≤ max* 对于某些公共`min`和`max`，证明者需要满足两个陈述， *x ≥ min* 和 *x ≤ max* 。 *x ≥ min* 相当于证明 *x - min ≥ 0* 和 *x ≤ max* 相当于证明 *max - x ≥ 0* 。现在，如果证明者对于某些提交的`v`、`v` ≥ 0，他可以证明 *x - min ≥ 0* 和 *max - x ≥ 0* ，并对 *x - min* 和 *max - x.* 进行提交，那么让我们重点证明`v` ≥ 0 且小于最大允许值，以避免某些提交的`v`，即`v`在 *[0，MAX_VALUE 如果证明者创建了一个`v`的位表示，并且该位表示包含了`n`位，那么很明显`v`位于 2ⁿ).的[0 一旦证明者创建了这个`n`位向量，它仍然不能向验证者透露这个位向量，因为这将`v`给验证者。但是如果证明者可以向验证者证明这个向量的每个元素确实是一个比特，并且这些比特被设置在“正确的索引”处，那么它就可以说服验证者`v`在范围内。
说 n 位向量是【bₙ₋₁，bₙ₋₂，…b₁，b₀].要证明每个 bᵢ都有一点，证明 bᵢ*(1-bᵢ)=0.就足够了下面的片段展示了这个想法**

```
*for i in 0..n {
    // Create low-level variables and add them to constraints
    let (a, b, o) = cs.allocate(|| {
        let q: u64 = v.assignment.ok_or(R1CSError::*MissingAssignment*)?;
        let bit: u64 = (q >> i) & 1;
        *Ok*(((1 - bit).into(), bit.into(), Scalar::*zero*()))
    })?;

    // Enforce a * b = 0, so one of (a,b) is zero
    cs.constrain(o.into());

    // Enforce that a = 1 - b, so they both are 1 or 0.
    cs.constrain(a + (b - 1u64));
}*
```

*上面的例子迭代了`v`的 n 位。`cs`是约束系统。前面例子中的证明者和验证者都是约束系统，都将运行上面的代码块。`allocate`将分配 3 个变量，`a`，`b`和`o`，约束条件为`a * b = o`。变量`a`和`b`也被分别赋值`1-bit`和`bit`。因此，对于`v`的每个位`bit`(`(q >> i) & 1`返回第`i`个最低有效位)，证明者将满足约束`(1-bit)*bit=o`并将约束`o`为`cs.constrain(o.into())`中的 0。但是如果你注意到传递给`allocate`的闭包可以访问`v`的值，这对于验证器来说是不可能的，事实上也不是。闭包只为证明者执行，不为验证者执行。但是这意味着可以将任意值赋给`a`和`b`，因为只有`o`被约束为 0，例如 a=3 和 b=0 仍然导致 a*b=0。这样就需要一个额外的约束，即`a = 1 - b` = > `cs.constrain(a + (b — 1u64))`
上面的例子证明了向量的那个元素是位，但仍然不能证明位向量是针对`v`的。例如，上述约束系统将满足全 0 或全 1，但这可能不是`v`的正确表示。我们需要更多的约束来证明这个位向量是用于`v`的。*

```
*pub fn positive_no_gadget<CS: ConstraintSystem>(
    cs: &mut CS,
    v: AllocatedQuantity,
    n: usize
    ,) -> Result<(), R1CSError> {
    let mut constraint_v = vec![(v.variable, -Scalar::*one*())];
    let mut exp_2 = Scalar::*one*();
    for i in 0..n {
        // Create low-level variables and add them to constraints
        let (a, b, o) = cs.allocate(|| {
            ....
        })?;
        // Enforce a * b = 0, so one of (a,b) is zero
        // Enforce that a = 1 - b, so they both are 1 or 0.
        ....
        constraint_v.push((b, exp_2)  );
        exp_2 = exp_2 + exp_2;
    }

    // Enforce that -v + Sum(b_i * 2^i, i = 0..n-1) = 0 => Sum(b_i * 2^i, i = 0..n-1) = v
    cs.constrain(constraint_v.iter().collect());

    *Ok*(())
}*
```

*我们从线性组合的向量`constraint_v`开始，负的`v`是它的第一项。然后对于`v`的每一位，我们将该位乘以适当的 2 的幂，并将结果加到`constraint_v.push((b, exp_2))`中的上述线性组合中。最后，对线性组合的所有项求和，结果为 0。`cs.constrain(constraint_v.iter().collect())`表示将`constraint_v`的所有项相加，并确保总和为 0。类型`AllocatedQuantity`是提交变量的包装器。完整的代码是[这里是](https://github.com/lovesh/bulletproofs/blob/aa2b0a5e7815080c0125d321903ad160a6409689/tests/utils.rs#L85)。
现在我们可以用上面的`positive_no_gadget`来证明`x-min`和`max — x`都在[0，2ⁿ).这看起来很好，但证明者仍然可以欺骗。由于证明者只对`x-min`和`max-x`给出承诺，他可以通过在承诺中使用不同的`x`来欺骗，因此一个承诺在`x-min`之上，另一个在`max-y`之上。这可以通过确保将两个提交值相加得到结果`max-min`来解决。设 a = x-min，b=max-x，证明者承诺`v`、`a`和`b`。*

```
*let (com_v, var_v) = prover.commit(v.into(), Scalar::*random*(&mut rng));
let quantity_v = AllocatedQuantity {
    variable: var_v,
    assignment: *Some*(v),
};let (com_a, var_a) = prover.commit(a.into(), Scalar::*random*(&mut rng));
let quantity_a = AllocatedQuantity {
    variable: var_a,
    assignment: *Some*(a),
};let (com_b, var_b) = prover.commit(b.into(), Scalar::*random*(&mut rng));
let quantity_b = AllocatedQuantity {
    variable: var_b,
    assignment: *Some*(b),
};*
```

*校准者将`v`、`a`和`b`的变量传递给`bound_check_gadget`，确保`v`在`min`、`max`中。然后他创造证据。*

```
*assert!(bound_check_gadget(&mut prover, quantity_v, quantity_a, quantity_b, max, min, n).is_ok());

let proof = prover.prove()?;*
```

*`bound_check_gadget`确保 a+b = max-min 且 a 和 b 都在[0，2ⁿ).完整的代码是[这里是](https://github.com/lovesh/bulletproofs/blob/0b1a76c28641d79765c937df60b776c8ce48cb77/tests/gadget_bound_check.rs)。*

```
*pub fn bound_check_gadget<CS: ConstraintSystem>(
    cs: &mut CS,
    v: AllocatedQuantity,
    a: AllocatedQuantity,
    b: AllocatedQuantity,
    max: u64,
    min: u64,
    n: usize
) -> Result<(), R1CSError> {
    // a + b = max - min
    let lc_max_minus_min: LinearCombination = vec![(Variable::*One*(), Scalar::*from*(max-min))].iter().collect();

    // Constrain a + b to be same as max - min.
    cs.constrain(a.variable + b.variable - lc_max_minus_min);

    // Constrain a in [0, 2^n)
    assert!(positive_no_gadget(cs, a, n).is_ok());
    // Constrain b in [0, 2^n)
    assert!(positive_no_gadget(cs, b, n).is_ok());

    *Ok*(())
}*
```

## *承诺值非零的证明*

*经常需要证明某个值是非零的，而不需要揭示它，只需要提交它，我们将在下面的集合非成员证明中看到它的用法。另一种用法是证明某个值不等于某个值，因为证明`x`不等于`c`，证明`x-c`不等于 0 就足够了。这个证明是基于下面的观察(不是我的)。说你要证明`x`不等于 0。计算`x`的逆，称之为`inv`。让`y = if x!=0 then 1 else 0`。`inv` = 0 当`x` =0 否则`inv` = `x` ⁻。现在以下两个等式成立:I)x *(1-y)= 0(T27)ii)x * inv = y(T28)证明者首先提交`x`及其逆`inv`，然后满足以上两个约束。*

```
*let (com_val, var_val) = prover.commit(value.clone(), Scalar::*random*(&mut rng));
let alloc_scal = AllocatedScalar {
    variable: var_val,
    assignment: *Some*(value),
};let inv = value.invert();
let (com_val_inv, var_val_inv) = prover.commit(inv.clone(), Scalar::*random*(&mut rng));
let alloc_scal_inv = AllocatedScalar {
    variable: var_val_inv,
    assignment: *Some*(inv),
};
assert!(is_nonzero_gadget(&mut prover, alloc_scal, alloc_scal_inv).is_ok());*
```

*以下是一些限制因素*

```
*pub fn is_nonzero_gadget<CS: ConstraintSystem>(
    cs: &mut CS,
    x: AllocatedScalar,
    x_inv: AllocatedScalar,
) -> Result<(), R1CSError> {
    let y: u32 = 1;

    let x_lc: LinearCombination = vec![(x.variable, Scalar::*one*())].iter().collect();
    let one_minus_y_lc: LinearCombination = vec![(Variable::*One*(), Scalar::*from*(1-y))].iter().collect();
    let y_lc: LinearCombination = vec![(Variable::*One*(), Scalar::*from*(y))].iter().collect();

    // x * (1-y) = 0
    let (_, _, o1) = cs.multiply(x_lc.clone(), one_minus_y_lc);
    cs.constrain(o1.into());

    // x * x_inv = y
    let inv_lc: LinearCombination = vec![(x_inv.variable, Scalar::*one*())].iter().collect();
    let (_, _, o2) = cs.multiply(x_lc.clone(), inv_lc.clone());
    // Output wire should have value `y`
    cs.constrain(o2 - y_lc);

    // Ensure x_inv is the really the inverse of x by ensuring x*x_inv = 1
    let (_, x_inv_var, o3) = cs.multiply(x_lc, inv_lc);
    // Output wire should be 1
    let one_lc: LinearCombination = vec![(Variable::*One*(), Scalar::*one*())].iter().collect();
    cs.constrain(o3 - one_lc);
    *Ok*(())
}*
```

*注意，前两个约束`cs.constrain(o1.into())`和`cs.constrain(o2 — y_lc)`对应于上面的两个等式，但还有第三个约束检查`inv`是否实际上是`x`的逆。这是必要的，因为证明者只将`x`和`inv` ( `x` ⁻)的承诺给验证者，并且证明者可能通过不给出相反的值而给出其他值来欺骗。所以验证者确保`inv`实际上是逆的。完整的代码是[这里是](https://github.com/lovesh/bulletproofs/blob/01f36edcc09a553762bcb519d41bc92bb0727498/tests/gadget_zero_nonzero.rs#L65)。*

## *证明承诺值不等于给定值*

*证明你的值`v`不等于给定的`c`和证明
`v — c != 0`是一样的。所以我们利用上面的`is_nonzero_gadget`。完整的代码是[这里是](https://github.com/lovesh/bulletproofs/blob/range-proof/tests/gadget_not_equals.rs)。*

## *设置非成员*

*证明者可能需要证明他的承诺值不在集合中，例如，假设有一个公共集合`S` as [2，9，78，44，55]并且证明者的承诺值`v`是 12，他不希望验证者知道他的值是 12，而只是知道他的值不在集合中。其思想是证明者可以从每个集合元素中减去他的值，并证明每个结果都是非零的，即对于每个集合索引`i`。所以证明者首先提交他的值，然后提交每个集合元素和他的值的差，以及每个差的倒数，`2*n+1`总共提交，其中`n`是集合中元素的数量。需要对差值的倒数进行承诺，以证明差值*非零*，如前一示例所示。*

```
*let mut comms: Vec<CompressedRistretto> = vec![];
let mut diff_vars: Vec<AllocatedScalar> = vec![];
let mut diff_inv_vars: Vec<AllocatedScalar> = vec![];let (com_value, var_value) = prover.commit(value.clone(), Scalar::*random*(&mut rng));
let alloc_scal = AllocatedScalar {
    variable: var_value,
    assignment: *Some*(value),
};for i in 0..set_length {
    let elem = Scalar::*from*(set[i]);
    let diff = elem - value;
    let diff_inv = diff.invert();

    // Take difference of set element and value, `set[i] - value`
    let (com_diff, var_diff) = prover.commit(diff.clone(), Scalar::*random*(&mut rng));
    let alloc_scal_diff = AllocatedScalar {
        variable: var_diff,
        assignment: *Some*(diff),
    };
    diff_vars.push(alloc_scal_diff);
    comms.push(com_diff);

    // Inverse needed to prove that difference `set[i] - value` is non-zero
    let (com_diff_inv, var_diff_inv) = prover.commit(diff_inv.clone(), Scalar::*random*(&mut rng));
    let alloc_scal_diff_inv = AllocatedScalar {
        variable: var_diff_inv,
        assignment: *Some*(diff_inv),
    };
    diff_inv_vars.push(alloc_scal_diff_inv);
    comms.push(com_diff_inv);
}assert!(set_non_membership_gadget(&mut prover, alloc_scal, diff_vars, diff_inv_vars, &set).is_ok());*
```

*与上述承诺相对应的变量用于以下约束条件中*

```
*pub fn set_non_membership_gadget<CS: ConstraintSystem>(
    cs: &mut CS,
    v: AllocatedScalar,
    diff_vars: Vec<AllocatedScalar>,
    diff_inv_vars: Vec<AllocatedScalar>,
    set: &[u64]
) -> Result<(), R1CSError> {
    let set_length = set.len();

    for i in 0..set_length {
        // Take difference of value and each set element, `v - set[i]`
        let elem_lc: LinearCombination = vec![(Variable::*One*(), Scalar::*from*(set[i]))].iter().collect();
        let v_minus_elem = v.variable - elem_lc;

        // Since `diff_vars[i]` is `set[i] - v`, `v - set[i]` + `diff_vars[i]` should be 0
        cs.constrain(diff_vars[i].variable + v_minus_elem);

        // Ensure `set[i] - v` is non-zero
        is_nonzero_gadget(cs, diff_vars[i], diff_inv_vars[i])?;
    }

    *Ok*(())
}*
```

*注意，在上述约束中，除了非零约束之外，每个集合元素还有附加约束。需要这个约束来确保校准者对每个设置的索引`i`使用`S[i] — v != 0`中相同的`v`。这是通过为`v-S[i]`创建一个变量，并将其添加到上述差值中，并将结果约束为 0 来实现的。完整的代码是[这里是](https://github.com/lovesh/bulletproofs/blob/01f36edcc09a553762bcb519d41bc92bb0727498/tests/gadget_set_non_membership.rs)。*

## *设置成员证明*

*证明者可能需要证明他的承诺值在集合`S`中，但不需要透露他的值`v`。这是这个想法(摘自 [ethsnarks](https://github.com/HarryR/ethsnarks/blob/master/src/gadgets/one_of_n.hpp) repo)
1。证明者创建对应于集合的位向量，其中集合的每个元素 1 位。所有位都是 0。
2。证明者将位向量中的位设置在其值`v`的索引处。例如，如果 set `S`是[5，9，1，100，200]并且证明者的值是 100，则位向量将是[0，0，0，1，0]。
3。证明者然后证明
i)比特向量的每个元素实际上是一个比特，
ii)通过将所有比特相加并且证明总和为 1，比特向量中只有 1 个比特集合。
iii)对于每个设定指标`i`，该关系成立`set[i] * bitvec[i] = bitvec[i] * v`。这应该成立，因为除了索引`v`之外，所有`i`的`bitvec[i]`都是 0。*

*证明者首先创建位向量并提交给每一位。*

```
*let bit_map: Vec<u64> = set.iter().map( | elem | {
    if *elem == value { 1 } else { 0 }
}).collect();let mut comms = vec![];
let mut bit_vars = vec![];
for b in bit_map {
    let (com, var) = prover.commit(b.into(), Scalar::*random*(&mut rng));
    let quantity = AllocatedQuantity {
        variable: var,
        assignment: *Some*(b),
    };
    assert!(bit_gadget(&mut prover, quantity).is_ok());
    comms.push(com);
    bit_vars.push(quantity);
}*
```

*为了证明某个值是位，使用了基于关系式`bit*(1-bit)=0`的`bit_gadget`。*

```
*pub fn bit_gadget<CS: ConstraintSystem>(
    cs: &mut CS,
    v: AllocatedQuantity
) -> Result<(), R1CSError> {
    let (a, b, o) = cs.allocate(|| {
        let bit: u64 = v.assignment.ok_or(R1CSError::*MissingAssignment*)?;
        *Ok*(((1 - bit).into(), bit.into(), Scalar::*zero*()))
    })?;

    // Variable b is same as v so b + (-v) = 0
    let neg_v: LinearCombination = vec![(v.variable, -Scalar::*one*())].iter().collect();
    cs.constrain(b + neg_v);

    // Enforce a * b = 0, so one of (a,b) is zero
    cs.constrain(o.into());

    // Enforce that a = 1 - b, so they both are 1 or 0.
    cs.constrain(a + (b - 1u64));

    *Ok*(())
}*
```

*请注意，需要`cs.constrain(b + neg_v)`来确保证明者为变量`b`分配与`v`中提交的值相同的值。*

*现在，证明者通过对向量求和并将结果与 1 进行比较，来确保向量中只有 1 位设置。*

```
*assert!(vector_sum_gadget(&mut prover, &bit_vars, 1).is_ok());*
```

*下面是一个矢量和小工具，可以用来比较给定矢量和给定值。*

```
*// Ensure sum of items of `vector` is `sum`
pub fn vector_sum_gadget<CS: ConstraintSystem>(
    cs: &mut CS,
    vector: &[AllocatedQuantity],
    sum: u64
) -> Result<(), R1CSError> {
    let mut constraints: Vec<(Variable, Scalar)> = vec![(Variable::*One*(), -Scalar::*from*(sum))];
    for i in vector {
        constraints.push((i.variable, Scalar::*one*()));
    }

    cs.constrain(constraints.iter().collect());

    *Ok*(())
}*
```

*注意，线性组合`constraints`的第一项是负的`sum`，并且该线性组合的所有项的总和被约束为 0。*

*现在，证明者通过提交值并使用已经为提交的位向量分配的变量来满足最终乘积关系`set[i] * bitvec[i] = bitvec[i] * v`。*

```
*let (com_value, var_value) = prover.commit(value.into(), Scalar::*random*(&mut rng));
let quantity_value = AllocatedQuantity {
    variable: var_value,
    assignment: *Some*(value),
};
assert!(vector_product_gadget(&mut prover, &set, &bit_vars, &quantity_value).is_ok());*
```

*`vector_product_gadget`*

```
*// Ensure items[i] * vector[i] = vector[i] * value
pub fn vector_product_gadget<CS: ConstraintSystem>(
    cs: &mut CS,
    items: &[u64],
    vector: &[AllocatedQuantity],
    value: &AllocatedQuantity
) -> Result<(), R1CSError> {
    let mut constraints = vec![(value.variable, -Scalar::*one*())];

    for i in 0..items.len() {let (a, b, o) = cs.allocate(|| {
            let bit: u64 = vector[i].assignment.ok_or(R1CSError::*MissingAssignment*)?;
            let val = value.assignment.ok_or(R1CSError::*MissingAssignment*)?;
            *Ok*((items[i].into(), bit.into(), (bit*val).into()))
        })?;

        constraints.push((o, Scalar::*one*()));

        let item_var: LinearCombination = vec![(Variable::*One*(), items[i].into())].iter().collect();
        cs.constrain(a - item_var);

        // Each `b` is already constrained to be 0 or 1
    }

    // Constrain the sum of output variables to be equal to the value of committed variable
    cs.constrain(constraints.iter().collect());

    *Ok*(())
}*
```

*注意约束`cs.constrain(a — item_var)`。验证者确保由证明者分配给变量`a`的值等于该索引处的集合项的值。完整的代码是[这里是](https://github.com/lovesh/bulletproofs/blob/548f71b31abf4079e1a68ae8322233b540f0ab5e/tests/gadget_set_membership.rs)。
还有另外一种证明集合成员关系的方法*。它依赖于这样的思想:如果一个元素存在于集合中，那么通过取每个集合项和这个元素的差而构造的向量将包含 1 个零值。所以如果我们把这个新向量的所有元素相乘，乘积将是 0。证明者承诺每个集合项的值及其差值，并调用新的集合成员小工具`set_membership_1_gadget`**

```
*let (com_value, var_value) = prover.commit(value.clone(), Scalar::*random*(&mut rng));
let alloc_scal = AllocatedScalar {
    variable: var_value,
    assignment: *Some*(value),
};
comms.push(com_value);

for i in 0..set_length {
    let elem = Scalar::*from*(set[i]);
    let diff = elem - value;

    // Take difference of set element and value, `set[i] - value`
    let (com_diff, var_diff) = prover.commit(diff.clone(), Scalar::*random*(&mut rng));
    let alloc_scal_diff = AllocatedScalar {
        variable: var_diff,
        assignment: *Some*(diff),
    };
    diff_vars.push(alloc_scal_diff);
    comms.push(com_diff);
}

assert!(set_membership_1_gadget(&mut prover, alloc_scal, diff_vars, &set).is_ok());*
```

*在`set_membership_1_gadget`中，`diff_vars`元素的乘积被约束为 0。完整的代码是[这里是](https://github.com/lovesh/bulletproofs/blob/range-proof/tests/gadget_set_membership_1.rs)。*

```
*pub fn set_membership_1_gadget<CS: ConstraintSystem>(
    cs: &mut CS,
    v: AllocatedScalar,
    diff_vars: Vec<AllocatedScalar>,
    set: &[u64]
) -> Result<(), R1CSError> {
    let set_length = set.len();
    // Accumulates product of elements in `diff_vars`
    let mut product: LinearCombination = Variable::*One*().into();

    for i in 0..set_length {
        // Take difference of value and each set element, `v - set[i]`
        let elem_lc: LinearCombination = vec![(Variable::*One*(), Scalar::*from*(set[i]))].iter().collect();
        let v_minus_elem = v.variable - elem_lc;

        // Since `diff_vars[i]` is `set[i] - v`, `v - set[i]` + `diff_vars[i]` should be 0
        cs.constrain(diff_vars[i].variable + v_minus_elem);

        let (_, _, o) = cs.multiply(product.clone(), diff_vars[i].variable.into());
        product = o.into();
    }

    // Ensure product of elements if `diff_vars` is 0
    cs.constrain(product);

    *Ok*(())
}*
```

> *[直接在您的收件箱中获得最佳软件交易](https://coincodecap.com/?utm_source=coinmonks)*

*[![](img/7c0b3dfdcbfea594cc0ae7d4f9bf6fcb.png)](https://coincodecap.com/?utm_source=coinmonks)*