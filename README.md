

<h1> <img src="documentation/others/kam1n0.png" width="45" />   What Is Kam1n0? </h1>

![image](https://img.shields.io/badge/license-Apache%202.0-brightgreen.svg?style=flat-square) 
![GitHub (pre-)release](https://img.shields.io/badge/kam1n0%20release-v2.0.0-orange.svg?style=flat-square)
![Github All Releases](https://img.shields.io/github/downloads/McGill-DMaS/Kam1n0-Plugin-IDA-Pro/total.svg?style=flat-square&&maxAge=86400)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)


**_Kam1n0 v2.x_** is a scalable assembly management and analysis platform. It allows a user to first index a (large) collection of binaries into different repositories and provide different analytic services such as clone search. It supports multi-tenancy access and management of assembly repositories by introducing the concept of **_Application_**. An application instance contains its own exclusive repository and provides a specialized analytic service. Considering the versatility of reverse engineering tasks, Kam1n0 v2.x server currently provides three different types of clone-search applications: **_Asm-Clone_**, **_Sym1n0_**, and **_Asm2Vec_**. New application type can be further added to the platform. 

<p align="center">
  <img src="documentation/others/stack.png"/> 
</p>

A user can create applications instance of any chosen application type ann own unlimited number of applications. An application can be shared among a specific group of users. Application repository read-write access and on-off status can be controlled by the application owner. Kam1n0 v2.x server will serve the applications concurrently using a resource pool. 


Kam1n0 was developed by Steven H. H. Ding and Miles Q. Li under the supervision of Benjamin C. M. Fung of the [Data Mining and Security Lab](http://dmas.lab.mcgill.ca/) at McGill University in Canada. It won the second prize at the [Hex-Rays Plug-In Contest 2015](https://hex-rays.com/contests/2015/). If you find Kam1n0 useful, please cite our paper:

* S. H. H. Ding, B. C. M. Fung, and P. Charland. [Kam1n0: MapReduce-based Assembly Clone Search for Reverse Engineering](https://drive.google.com/file/d/0BzRSjM7kjy-rZWUtRnFXR0ZpSjg/view?usp=sharing). In <i>Proceedings of the 22nd ACM SIGKDD International Conference on Knowledge Discovery and Data Mining (SIGKDD)</i>, pages 461-470, San Francisco, CA: ACM Press, August 2016.

## Asm-Clone

Asm-Clone applications try to solve the efficient subgraph search problem (i.e. graph isomorphism problem) for assembly functions (<1.3s average query time and <30ms average index time with 2.3M functions). Given a target function (the one on the left as shown below), it can identity the cloned subgraphs among other functions in the repository (the one on the  right as shown below).

* Application Type: Asm-Clone
* The original clone search service used in Kam1n0 v1.x.
* Currently support Meta-PC, ARM, PowerPC, and TMS320c6 (experimental).
* Support subgraph clone search within a certain assembly code family.
  * \+ Good interpretability of the result: breaks down to subgraphs.
  * \+ Accurate for searching within the given code family.
  * \+ Good for differing various patches or versions for big binaries.
  * \- Relatively more sensitive to instruction set changes, optimizations, and obfuscation.
  * \- Need to pre-define the syntax of the assembly code language.
  * \- Need to have assembly code of the same chosen family in the repository.


<p align="center">
  <img src="documentation/others/asm-clone.png"/> 
</p>


## Sym1n0

Semantic clone search by differentiated fuzzing testing and constraint sovlving. An efficient and scalable dynamic-static hybrid approach (<1s average query time and <100ms average index time with 1.5M functions). Given a target function (the one on the left as shown below), it can identity the cloned subgraphs among other functions in the repository (the one on the  right as shown below). Support visualization of abstract syntax graph.
* Application Type: Sym1n0 (v2 only)
* Clone search by both symbolic execution and concrete execution. 
* Differentiate functions based on their different I/O behavior.
* Clone search conducted on the abstract syntax graph constructed from Vex IR (powered by LibVex). 
  * \+ Clone search across different assembly code families.
    * For example, indexed x86 binaries but the query is ARM code. 
  * \+ Subgraph clone search.
  * \+ Support a wide range of families .
    * x86, AMD64, MIPS32, MIPS64, PowerPC32, PowerPC64, ARM32, and ARM64.
  * \+ An efficient dynamic-static hybrid approach.
  * \+ Ideal for analyzing firmware compiled for different processors.
  * \- Sensitive to heavy graph manipulation (such as a full flattening).
  * \- Sensitive to large scale breakdown of basic block integrity. 



<p align="center">
  <img src="documentation/others/sym1n0.png"/> 
</p>


## Ams2Vec

Asm2Vec leverages representation learning. It understands the lexical semantic relationship of assembly code. For example, `xmm*` regiters are semantically related to vector operations such as `addps`. `memcpy` is similar to `strcpy`. The graph below shows different assembly functions compiled from the same source code of gmpz tdiv r 2exp in libgmp. From left to right, the assembly functions are compiled with gcc O0 option, gcc O3 option, LLVM obfuscator Control Flow Graph, Flattening option, and LLVM obfuscator Bogus Control Flow Graph option. Asm2Vec can **_statically_** identify them as clones.

* Leverage representation learning.
* Understand the lexical semantic relationship of assembly code.
  * \+ State-of-the-art for clone search against heavy code obfuscation techniques.
    * (>0.8 accuracy for all options applied in O-LLVM, multple iterations).
  * \+ State-of-the-art for clone search against code optimization.
    * (>0.8 accuracy between O0 and O3, >0.94 accuracy between O2 and O3)
  * \+ Even better result than the most recent dynamic approach.
  * \+ It is much more efficient than recent dynamic approaches. 
  * \+ Do not need to define the architecture. It learns by reading any code.
  * \+ Static approach: efficient and scalable.
  * \- No subgraphs.
  * \- Assumes that all assembly code belongs to the same processor family.
  * \- Static approach: cannot recognize jump table etc.


<p align="center">
  <img src="documentation/others/asm2vec.png"/> 
</p>

## Platform

<p align="center">
  <img src="documentation/others/pic2.png"/> 
</p>

In this repository, we release the initial version of Kam1n0 and its IDA Pro plug-in. It can run on a single workstation/server and provides a clone search service through RESTful web services. Users can connect to the server through IDA Pro. Alternatively, it can be deployed on a distributed cluster (next major release).

##  Release note 2017-02-09 1.1.0
* [Kam1n0 Core] Added a new symbolic mode. Now it supports cross-architecture subgraph clone search on the symbolic expression level. Included libvex and z3 library. Supported architecture: x86, AMD64, MIPS32, MIPS64, PowerPC32, PowerPC64, ARM32, and ARM64.
* [Kam1n0 Core] Updated graph search algorithm. Improved scalability & accuracy. Updated default ALSH settings.
* [Kam1n0 Core] Added Visual C++ Redistributable for VS15 dependency (included in the installer for z3).
* [Web UI] In the symbolic mode, we also visualize the control flow graph with abstract syntax tree for each basic block.
* [Web UI] User can index multiple files at a time. 
* [Web UI] User can directly index idb or i64 file.
* [Web UI] Fixed web UI bugs and improved usability.
* [Web UI] User can interrupt running jobs through the administration portal.
* [RESTful API] The old API is no longer working. Check out new API after installation.
* [IDA Pro plug-in for Kam1n0] Support composition analysis query. 

##  Release note 2016-05-03 1.0.0-rc1
* [Web UI] Added a web interface for clone search of an assembly function.
* [Web UI] Added a web interface for clone search of a binary file.
* [Kam1n0 Workbench] Multiple repositories can be created and managed on a single workstation.
* [Kam1n0 Core] The clone search results file can be shared and browsed on another machine without access to the repository.
* [Kam1n0 Core] Support indexing and searching of large binary files (>40 MB) without limits on system memory.
* [Kam1n0 Core] Support ARM, PowerPC, x86, and AMD64 binaries.
* [Kam1n0 Core] Support user-defined processor architectures.
* [Kam1n0 Core] Optimized index structure provides better scalability and clone search quality.
* [Kam1n0 Core] Kam1n0 no longer skips basic blocks which have less than three instructions. Now, only single instruction basic blocks are skipped, thanks to the new index structure.
* [IDA Pro plug-in for Kam1n0] [Experimental] Added an assembly fragment search functionality. 
* [IDA Pro plug-in for Kam1n0] Added a tree view for browsing a large number of clones.

## Compatibility

* The assembly code repositories and configuration files used in previous versions (<1.0.0) are no longer supported by the latest version. See the documentation on how to migrate previous repositories. 

## Scalability

* You can index millions of functions in each repository on a single machine. The average response time for a query still stays around 1 s and the average indexing time for a function, around 20 ms.

#  Installation

The current release of Kam1n0 consists of two installers: The core server and IDA Pro plug-in. 

<table>
  <tr>
    <th>Installer</th>
    <th>Included components</th>
    <th>Description</th>
  </tr>
  <tr>
    <td rowspan="4">Kam1n0-server.msi</td>
     <td>Core engine</td>
     <td>Main engine providing service for indexing and searching.</td>
  </tr>
   <tr>
      <td>Workbench</td>
     <td>A user interface to manage the repositories and running service.</td>
  </tr>
 <tr>
      <td>Web user interface</td>
     <td>Web user interface for searching/indexing binary files and assembly functions.</td>
  </tr>
  <tr>
     <td>Visual C++ redistributable for VS 15</td>
     <td>Dependecy for z3.</td>
  </tr>
  <tr>
    <td rowspan="3">Kam1n0-client-idaplugin.msi</td>
     <td>Plug-in</td>
     <td>Connectors and user interface.</td>
  </tr>
<tr>
     <td>Cefpython</td>
     <td>Rendering engine for the user interface.</td>
  </tr>
<tr>
     <td>Wxpython</td>
     <td>Rendering engine for Cefpython.</td>
  </tr>
</table>

## Installing the Kam1n0 Server

The Kam1n0 core engine is purely written in Java. You need the following dependencies:

* [Required] The latest x64 8.x JRE/JDK distribution from [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Optional] The latest version of IDA Pro with the [idapython](https://github.com/idapython/src/) plug-in installed. The Python plug-in and runtime should have already been installed with IDA Pro. Reinstall IDA Pro if necessary. 

Download the ```Kam1n0-server.msi``` file from our [release page](https://github.com/McGill-DMaS/Kam1n0-Plugin-IDA-Pro/releases). Follow the instructions to install the server. You will be prompted to select an installation path as well as the IDA Pro installation path. The latter is optional if the server does not have to deal with any disassembling. In other words, the client side  uses the Kam1n0 plugin for IDA Pro. It is strongly suggested to have the IDA Pro installed with the Kam1n0 server. The current version of Kam1n0 only supports IDA Pro.

## Installing the IDA Pro Plug-in

The Kam1n0 IDA Pro plug-in is written in Python for the logic and in HTML/JavaScript for the rendering. The following dependencies are needed for its installation:

* [Required] The latest version of IDA Pro with the [idapython](https://github.com/idapython/src/) plug-in installed. The Python plug-in and runtime should have already been installed with IDA Pro. Reinstall IDA Pro if necessary. 


Next, download the ```Kam1n0-client-idaplugin.msi``` installer from our [release page](https://github.com/McGill-DMaS/Kam1n0-Plugin-IDA-Pro/releases). Follow the instructions to install the plug-in and runtime. Please note that the plug-in has to be installed in the IDA Pro plugins folder which is located at ```$IDA_PRO_PATH$/plugins```. For example, on Windows, the path could be ```C:/Program Files (x86)/IDA 6.95/plugins```. The installer will validate the path. 

## Configuring the Kam1n0 Engine

In the previous version of Kam1n0, only a single repository was supported on a workstation and the configuration files for Kam1n0 were in the same folder than the engine executable file. Starting from 1.x.x version, Kam1n0 supports multiple repositories on a workstation and each repository can support different processor architectures. Each repository is given a data folder where you can find its configuration files. More details can be found in the Kam1n0 workbench tutorial.

# Documentation
* [Kam1n0 Server Tutorial](documentation/server/server.md#tutorial)
  * [Configuration and Engine Startup](documentation/server/server.md#edit-and-start-engine)
  * [Register an account and login](documentation/server/server.md#register-an-account-and-login)
  * [Create an application](documentation/server/server.md#create-an-app)
  * [Application Sharing and Access Control](documentation/server/server.md#share-an-app)
  * [Preparing the data](documentation/server/server.md#preparing-the-data)
  * [The application URL for IDA Pro Plugin](documentation/server/server.md#get-the-url-for-ida-pro-plugin)
  * [Index binary files](documentation/server/server.md#index-binary-files)
  * [Search with an assembly function](documentation/server/server.md#search-with-an-assembly-function)
    * [Flow graph view](documentation/server/server.md#flow-graph-view)
    * [Text diff view](documentation/server/server.md#text-diff-view)
    * [Clone group view](documentation/server/server.md#clone-group-view)
  * [Search with a binary file](documentation/server/server.md#search-with-a-binary-file)
  * [Browse a clone search result](documentation/server/server.md#browse-a-clone-search-result)
    * [The summary boxes](documentation/server/server.md#the-summary-boxes)
    * [Details](documentation/server/server.md#details)
* [IDA Pro Plug-in Tutorial](documentation/ida-pro-plugin/ida-pro-plugin.md#tutorial)
  * [Functionalities](documentation/ida-pro-plugin/ida-pro-plugin.md#functionalities)
  * [Walk through example](documentation/ida-pro-plugin/ida-pro-plugin.md#walk-through-example)
    * [Preparing the data](documentation/ida-pro-plugin/ida-pro-plugin.md#preparing-the-data)
    * [Engine startup and application URL](documentation/ida-pro-plugin/ida-pro-plugin.md#start-the-engine-and-get-the-url-for-ida-pro-plugin)
    * [Connection configuration](documentation/ida-pro-plugin/ida-pro-plugin.md#set-up-connection)
    * [Indexing from plug-in](documentation/ida-pro-plugin/ida-pro-plugin.md#indexing)
    * [Functions search](documentation/ida-pro-plugin/ida-pro-plugin.md#functions-search)
    * [Composition analysis](documentation/ida-pro-plugin/ida-pro-plugin.md#composition-analysis)
    * [Assembly fragment search](documentation/ida-pro-plugin/ida-pro-plugin.md#assembly-fragment-search)
    * [Search box](documentation/ida-pro-plugin/ida-pro-plugin.md#search-box)
  * [How does the Plugin Work](documentation/ida-pro-plugin/ida-pro-plugin.md#how-does-the-plug-in-work)
    * [User Interface](documentation/ida-pro-plugin/ida-pro-plugin.md#user-interface)
    * [Synchronization](documentation/ida-pro-plugin/ida-pro-plugin.md#synchronization)
    * [Two-way Communication](documentation/ida-pro-plugin/ida-pro-plugin.md#communication)


## Licensing

The software was developed by Steven H. H. Ding and Miles Q. Li under the supervision of Benjamin C. M. Fung at the McGill Data Mining and Security Lab. It is distributed under the Apache License Version 2.0. Please refer to LICENSE.txt for details.

Copyright 2017 McGill University. 
All rights reserved.
