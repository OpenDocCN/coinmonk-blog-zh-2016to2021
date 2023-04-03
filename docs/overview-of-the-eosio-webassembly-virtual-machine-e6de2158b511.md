# EOSIO WebAssembly 虚拟机概述

> 原文：<https://medium.com/coinmonks/overview-of-the-eosio-webassembly-virtual-machine-e6de2158b511?source=collection_archive---------1----------------------->

EOSIO 程序通常用 C++编写，然后编译成智能合同，在 EOSIO 区块链上执行。

但是你知道 C++并不是唯一可以用来编写 EOSIO 程序的编程语言吗？一些项目正在开发 Python，甚至是基于 Solidity 的程序。这些都被编译成独立于平台的代码，可以在 EOSIO WASM 虚拟机(WAVM)上运行。

所以 WAVM 根本不需要知道任何关于高级编程语言(C++，Python，Solidity…