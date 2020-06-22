# Introduce a process for incubating speculative LLVM projects

*   Proposal: [LP-0002](TODO)
*   Author(s): [Stella Laurenzo](https://github.com/stellaraccident), [Chris Lattner](https://github.com/lattner)
*   Review Managers: TBD
*   Status: WIP

_During the review process, add the following fields as needed:_

*   Pitch Thread: [1](url for discussion) [2](url for discussion)
*   Decision Notes: Rationale, Additional Commentary
*   Previous Revision: [1](url for previous significant revision)
*   Previous Proposal: None


## Introduction

Today, we maintain a high bar for getting a new subproject into LLVM: first a subproject has to be built far enough along to “prove its worth” to be part of the LLVM monorepo (e.g. demonstrate community, etc).  Once conceptually approved, it needs to follow all of the policies and practices expected by an LLVM subproject.

This proposal describes an additional "incubation" project classification that is intended to increase the effectiveness of starting new LLVM aligned projects prior to formal integration as a new subproject.

## Motivation

The single entry-point for new subprojects is problematic for a few reasons: 

* It implicitly means that projects have to start *somewhere else* but proactively decide to follow LLVM design methodology and principles in the hope of being accepted.
* It is sometimes socially difficult to get these projects going because there are many other forces that could encourage other practices.
* Once the project gets to a point of critical mass with the “wrong” approach, it is very difficult and expensive to convert to the LLVM style, and from a social perspective, inertia sometimes leads to forking off to separate projects instead of folding back in to LLVM.

There are some historic examples to draw from where the existing process was leveraged to an effective end but with a high degree of uncertainty and lingering issues:

* Chris personally encountered this at Google with MLIR - “why aren’t you using Google coding standards?”. While MLIR is now a successfully integrated subproject, there are lingering issues such as a community split between the LLVM communication channels and those from the TensorFlow organization that sponsored it in addition to awkward/undefined criteria for what should continue to be developed within LLVM vs outside in the TensorFlow repositories.
* Reportedly, Flang and other projects have found the big step from outside to subproject status to be challenging.

There are also recent cases that have not resolved or could take advantage of an incubation stage (all MLIR-based as that is the author's bias):

* Some speculative new work in the "compiler's for hardware" world.
* [mlir-npcomp](https://github.com/google/mlir-npcomp) which is trying to develop for eventual inclusion of some components as an LLVM subproject.
* [mlir-emitc](https://github.com/iml130/mlir-emitc) which suffered from the "where do I mkdir" problem on an idea that needs some elaboration to determine whether it should be part of MLIR, pursued in some other way, etc.
* [google iree](https://github.com/google/iree) as an example of a project that "went another way" during its formative time and will be difficult/impossible to fold back in (and could still benefit from an incubation area to promote LLVM-bound-but-not-yet-proven compiler components into). Disclaimer: the author was one of the founders of IREE and wishes for better options in the future.
* Discussions on IREE's Discord channel specualting about the proper place to incubate some code and dialects that have been developed for bridging PyTorch and MLIR-based tools (this is an example of literally just needing to get some existing work into a place that multiple parties in the community can collaborate on it under the LLVM umbrella).
* Historic discussions about ONNX, TensorFlow HLO, and other frontend dialects which have been developed outside and face inertia and other hurdles to integrate more closely.

It is the author's opinion that in some of these situations, a new kind of "subproject" will ultimately emerge (i.e. a dialect repository), but it is extremely hard to envision the right kind of setup and project infrastructure that would be the most useful when all of these projects are disconnected from the community, at different phases of development, and (in some cases) at various levels of mis-alignment with LLVM's code guidelines. Having an official incubation path will reduce ambiguity, increase community involvement, and allow us to inspect/guide these projects to desired outcomes along the way to ends that are not obvious from the outset.

## Proposed solution

There are very long-standing and successful incubators that exist (e.g. [Apache Incubator](https://incubator.apache.org/)) and can serve as templates. However, as a new process for this community, a fairly simple process could be a good starting point:

Here is a sketch of how this could work:

* We maintain the same high bar to get into the LLVM monorepo, LLVM CI etc.  No change here.
* We have a very light-weight proposal process that allows people to create incubator projects in the LLVM organization, with no code up front.  The project would be required to have e.g. a charter document and README.
*  Such projects are required to follow the LLVM developer policy, coding standards, CoC, etc, but can define their own stability and evolution process, code owners, etc.
* When the project is ready to graduate, it would follow the existing process for becoming a first-class part of the mono repo.
* We have some policy on when to retire/delete projects, which can be ironed out the first time it comes up (e.g. start with a nomination).
* We could even try to help encourage new projects to include a ‘mentor’ that has experience with the LLVM project to help nudge things in the right direction and encourage proper development approach.

There are a number of practical issues that need to be resolved and would benefit from the incubator community setting out patterns that increase commonality and make things easier for the next one. As an example, [the MLIR standalone template](https://github.com/llvm/llvm-project/tree/master/mlir/examples/standalone) has been used/maintained by several of the motivating projects to create reasonable out of tree experiences that are somewhat uniform.

Other practical issues that may call for the development of additional process/infrastructure:

* Mechanics of who creates the LLVM organization Git repository and assigns initial permissions needs to be defined.
* Managing the LLVM dependency: Presumably, for many such projects, one of their largest dependencies will by LLVM itself. Common patterns and tools can ease this substantially. For example, many of the motivating projects have successfully been building against installed LLVM directories vs source directories and having a common setup could make it fairly light-weight to create a low-barrier CI setup where it is easy to attach new projects to be tested against a rolling pre-built LLVM (i.e. `mlir-npcomp` builds on the order of 60 files and is very lightweight once the LLVM dep is factored out). Anything done here should be strictly an opt-in "using some common tools can help" approach vs a constraint imposed on all projects.
* Even though incubating projects place no expectation of maintenance on developers in the monorepo, they can be a valuable source of "in the wild" intelligence for maintainers regarding actual usage of APIs/features/etc. Having some commonality on CI infra could, for example, allow a monorepo developer to quickly evaluate whether some API changes are likely to break people. While we would want to be careful about a creeping expectation of maintenance, this could be useful for detecting unintended breaking changes to people outside of the project and may serve as a tool that helps the LLVM project itself be easier for outside integrators to rely on from a stability standpoint.

## Impact On Other Projects

None: there is explicitly no expectation of maintenance or compatibility placed from incubating projects back to the monorepo.

If successful, this may be a vehicle for existing, local approaches to experimental components to move out of tree (i.e. experimental targets were brought up on the RFC thread).

## Frequently Asked Questions

* Who specifically is responsible for creating the git repository and assigning initial permissions?

(No answer yet)

* Do these incubator projects exist in the monorepo?

No. We propose creating new projects in the LLVM organization.

## Alternatives considered

* Do nothing: The current process has worked for many years, and it is possible that the friction is useful/a feature.
* Incubate projects in the monorepo itself.
  * *Pros*: Easy "mkdir" flow to get started. Version/dependency skew is not an issue.
  * *Cons*: 
    * Increases systemic overhead and expectation of maintenance.
    * In trying this both ways, it can be quite desirable/more efficient to be working in a small repo with a defined surface area (in cases tried).
    * Will provide creeping expectations of maturity on incubating projects before they are ready to pay the cost.

