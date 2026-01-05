# Chapter 7 Production-side Delay Queue

In this article, we will first introduce what a delay queue is and what it can be used for; how to properly use this feature and avoid pitfalls; then we will introduce the implementation principle of Pulsar's delay queue and its existing problems, and finally introduce how we solved this problem. All related code contributions have been merged into the community's main branch, and readers can choose an appropriate version to try these optimizations.

{% embed url="https://github.com/apache/pulsar/pull/23611" %}
