---
title: Riak 2.0
project: riak
version: 2.0.0+
document: guide
audience: beginner
keywords: [developers]
---

Riak version 2.0 includes deep changes affecting all facets of Riak along with a wide variety of new features unavailable in previous versions. Here, we'd like to briefly describe these new features and direct you to sections of the documentation that explain how you can put them to work in your Riak installation. For more in-depth implementation details, we suggest checking out the [version 2.0 release notes](https://github.com/basho/riak/blob/riak-2.0.0/RELEASE-NOTES.md).

If you're upgrading to Riak 2.0 from an earlier version, please be aware that all of the new features listed below are purely optional:

* **Riak Data Types** --- Riak's new CRDT-based [[Data Types]] can simplify modeling data in Riak, but are only used in buckets explicitly configured to use them.
* **Strong Consistency, Riak Security, and the New Riak Search** --- These are subsystems in Riak that must be explicitly turned on to work. If not turned on, they will have no impact on performance. Furthermore, the older Riak Search will continue to be included with Riak.
* **Security** --- [[Authentication and authorization]] can be enabled or disabled at any time.
* **Configuration management** --- Riak's [[configuration files]] have been streamlined into a single file named `riak.conf`. If you are upgrading, however, your existing `app.config` and `vm.args` files will still be recognized in version 2.0.
* **Bucket Types** --- While we strongly recommend [[using bucket types]] when creating new buckets, they are not required.
* **Dotted Version Vectors (DVVs)** --- This alternative to traditional [[vector clocks]] is enabled by default in all [[bucket types|Using Bucket Types]], but DVVs can be disabled by setting the `dvv_enabled` property to `false` on any bucket type.

In a nutshell, upgrading to 2.0 will change how you use Riak only if you want it to. But even if you don't plan on using the new features, there are a number of improvements that make upgrading a good choice, including the following:

* [[Cluster metadata]] --- This is a subsystem of Riak added in 2.0 that reduces the amount of inter-node gossip in Riak clusters, which can reduce network congestion.
* [[Active Anti-Entropy]] --- While Riak has had an Active Anti-Entropy (AAE) feature that is turned on by default since version 1.3, AAE performance has been improved in version 2.0.
* Bug patches --- A variety of bugs present in earlier versions have been identified and patched.

More on upgrading can be found in our [[Riak 2.0 upgrade guide|Upgrading to 2.0]].

## Riak Data Types

In distributed systems, there is an unavoidable trade-off between consistency and availability. This can complicate some aspects of application design if you're using Riak as a key/value store because the application is responsible for resolving conflicts between replicas of objects stored in different Riak nodes.

Riak 2.0 offers a new approach to this problem for a wide range of uses cases in the form of [[Riak Data Types|using data types]]. Instead of forcing the application to resolve conflicts, Riak offers five Data Types that can cut through some of the complexities of developing using Riak: [[flags|Data Types#Flags]], [[registers|Data Types#Registers]], [[counters|Data Types#Counters]], [[sets|Data Types#Sets]], and [[maps|Data Types#Maps]].

#### Relevant Docs

* [[Using Data Types]] explains how to use Riak Data Types on the application side, with usage examples for all five Data Types in all of Basho's officially supported clients (Java, Ruby, Python, and Erlang) and for Riak's HTTP interface
* [[Data Types]] explains some of the theoretical concerns that drive Riak Data Types and shares some details about how they are implemented under the hood in Riak

#### Video

[Data Structures in Riak](http://vimeo.com/52414903) by Basho engineers [Sean Cribbs](https://github.com/seancribbs) and [Russell Brown](https://github.com/russelldb).

## Riak Search 2.0 (codename: Yokozuna)

Riak Search 2.0 is a complete, top-to-bottom replacement for Riak Search, integrating Riak with [Apache Solr](https://lucene.apache.org/solr/)'s full-text search capabilities and supporting Solr's client query APIs.

#### Relevant Docs

* [[Using Search]] provides a broad-based overview of how to use the new Riak Search
* [[Search Schema]] shows you how to create and manage custom search schemas
* [[Search Details]] provides an in-depth look at the design considerations that went into the new Riak Search

#### Video

[Riak Search 2.0](https://www.youtube.com/watch?v=-c1eynVLNMo) by Basho engineer and documentarian [Eric Redmond](https://github.com/coderoshi).

## Strong Consistency

Riak is typically known as an AP system, favoring high availability and partition tolerance while sacrificing data consistency. In version 2.0, you have the option of applying strong consistency guarantees and thus of using Riak as a CP---consistent plus partition-tolerant---system for some (or perhaps all) of your data.

#### Relevant Docs

* [[Using Strong Consistency]] shows you how to enable Riak's strong consistency subsystem and to apply strong consistency guarantees to data stored in specified buckets
* [[Strong Consistency]] provides a theoretical treatment of how a strongly consistent system differs from an [[eventually consistent|Eventual Consistency]] system, as well as details about how strong consistency is implemented in Riak

#### Video

[Bringing Consistency to Riak](http://vimeo.com/51973001) by Basho engineer [Joseph Blomstedt](https://github.com/jtuple). You should also check out [part 2](https://www.youtube.com/watch?v=gXJxbhca5Xg).

## Security

In version 2.0, Riak enables you to manage both **authorization** to perform specific tasks, from GETs and PUTs to running MapReduce jobs to administering Riak Search, and **authentication** of Riak clients seeking access to Riak. Previously, securing Riak was restricted to the network level. Now, security measures can be applied to the internals of Riak itself and managed through a simple command-line interface.

#### Relevant Docs

* [[Authentication and Authorization]] explains how Riak Security can be enabled and disabled, how users and groups are managed, how authorization to perform certain operations can be granted and revoked, how security ciphers can be chosen, and more
* [[Managing Security Sources]] is an in-depth tutorial on how to implement Riak's four supported authentication sources: trusted networks, passwords, pluggable authentication modules, and certificates

#### Video

[Locking the Distributed Chicken Coop](https://www.youtube.com/watch?v=T6i8S6_dV7U) by Basho engineer [Andrew Thompson](https://github.com/Vagabond).

## Simplified Configuration Management

In older versions of Riak, a Riak node's configuration was determined by two separate files: `app.config` and `vm.args`. In Riak 2.0, you have the option of either continuing to use these files, which can be useful if you're upgrading to 2.0, or to manage configuration through a single `riak.conf` file in which parameters are set using the following syntax:

```riakconf
parameter.sub-parameter = setting
```

Based on Basho's [Cuttlefish](https://github.com/basho/cuttlefish) project, the new system is much simpler, leaving behind the Erlang syntax required in `app.config`.

<div class="note">
<div class="title">Note on upgrading</div>
Version 2.0 will support both the old and the new configuration system, in case you're upgrading.
</div>

#### Relevant Docs

* [[Configuration Files]] lists and describes all of the configurable parameters available in Riak 2.0, from configuring your chosen storage backend(s) to setting default bucket properties to controlling Riak's logging system and much more.

#### Video

Lightning talk on [Cuttlefish](https://www.youtube.com/watch?v=Z3hKKpOFOrg) by Basho engineer [Joe DeVivo](https://github.com/joedevivo).

## Bucket Types

In older versions of Riak, bucket properties were managed on a bucket-by-bucket, ad hoc basis. With bucket types, you can now create and manage whole configurations of bucket properties and apply them to buckets. This is a much more efficient way of configuring buckets. In addition, bucket types act as a third namespace in addition to buckets and keys.

#### Relevant Docs

* [[Using Bucket Types]] explains how to create, modify, and activate bucket types, as well as how the new system differs from the older, bucket properties-based system

#### Video

[Bucket Types and Config](https://www.youtube.com/watch?v=lZk8cD-qFHM) hangout with Basho engineers [Joe DeVivo](https://github.com/joedevivo) and [Jordan West](https://github.com/jrwest).

## Dotted Version Vectors

In prior versions of Riak, [[conflict resolution]] was managed using [[vector clocks]], which are a mechanism for tracking object update causality. Riak 2.0 has added support for dotted version vectors (DVVs). DVVs serve an analogous role to vector clocks but are more effective at containing [[sibling explosion|Vector Clocks#sibing-explosion]] and can thus reduce Riak cluster latency. The [[dotted version vectors]] doc explains some of the theoretical nuances behind the distinction between DVVs and vector clocks and offers instructions on implementing DVVs.

## New Client Libraries

While Basho offered official [[client libraries]] for Java, Ruby, Python, and Erlang for versions of Riak prior to 2.0, all four clients have undergone major changes in anticipation of the 2.0 release.

Language | Docs
:--------|:----
[Java](https://github.com/basho/riak-java-client) | [Javadoc](http://basho.github.io/riak-java-client/2.0.0-SNAPSHOT/)
[Ruby](https://github.com/basho/riak-ruby-client) | [GitHub](https://github.com/basho/riak-ruby-client)
[Python](https://github.com/basho/riak-python-client) | [Sphinx](http://basho.github.io/riak-python-client/)
[Erlang](https://github.com/basho/riak-erlang-client) | [EDocs](http://basho.github.io/riak-erlang-client/)

You will also notice that our documentation now features a wide variety of code samples from all four officially supported clients.