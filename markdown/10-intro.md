<!-- .slide: data-state="cover" id="template-cover-page" data-timing="20" data-menu-title="Cover slide" -->
<div class="title">
    <h1>Kernel lessons from writing a SaaS</h1>
    <h2>What I learnt by delivering HTML and JavaScript to browsers</h2>
</div>

<div class="date-location">14 February 2023</div>


<!-- .slide: data-state="normal" id="template-agenda" data-menu-title="Agenda" -->
# Contents / agenda

### How difficult can it be to write SaaS?

Technically not difficult unless you care about latency and costs.

* Super easy way with "nocode"
* The easy/smart way with Serverless
* Still quite easy way with Containers/VMs and managed database
* The way I'm doing it
* Other things I tried
* Lessons for kernel


<!-- .slide: data-state="normal" id="one" data-menu-title="One" -->
# Nocode

* Examples: Bubble, Zapier, Retool, Airtable, ...
* Should really be called visual coding; still require logic
* Impressive, but limited and slow
* Often free then suddenly very expensive (after your locked in)

### Actually would be useful for us

* Internal tooling
  - bug tagging
  - test result analysis
  - job orchestration
* Already some work done on this, but...


<!-- .slide: data-state="normal" id="two" data-menu-title="One" -->
# Serverless or FaaS

* Example: Vercel, Firebase
* Function as a Service
* Little or no DevOps
* No storage or long running processes
* Every request requires creating a DB connection
* Often free then suddenly very expensive (after your locked in)


<!-- .slide: data-state="normal" id="one" data-menu-title="One" -->
# Managed database

* Example: Upstash, Supabase, Firestore, DynamoDB
* No need to deal with HA clusters, scaling etc.
* Often priced based on confusing collection of metrics
* Often free then suddenly very expensive (after your locked in)
* Exception: Vultr


<!-- .slide: data-state="normal" id="one" data-menu-title="One" -->
# What I'm doing

* One container per VM with Fly.io
  - have there own init
  - Uses HashiCorp Nomad somehow
* One container with the NodeJS app and one with KeyDB
* Multiple instances in lhr, dfw and syd.
* NodeJS VMs are simple:
  + auto scaled on demand
  + no persistent storage
  + long running
* KeyDB is a HA cluster...


<!-- .slide: data-state="normal" id="one" data-menu-title="One" -->
# I also tried the Nanos unikernel

* Compatible with many Linux syscalls
* No processes only threads
* Can create and deploy a VM as easy as a container
* Image is smaller than Alpine linux container
* I Ported clone3 to [Nanos](https://richiejp.com/nanos-clone3-brk-and-nodejs)
* Turns out Redis/KeyDB forks to save to disk


<!-- .slide: data-state="normal" id="one" data-menu-title="One" -->
# I also tried NixOS

* Functional programming language used for
  - packaging
  - config
* Only reproducible builds
* reproducible developer sessions
* Whole production env can be expressed in a script
* can roll back updates/config changes


<!-- .slide: data-state="normal" id="one" data-menu-title="One" -->
# KeyDB

* Redis fork
* Allows master to master replication
* Eventual consistency
* Most reads and writes made on local node
* Necessarily atomic writes must go through central node
* HA cluster setup is simpler
* My HA setup still far from perfect


<!-- .slide: data-state="normal" id="one" data-menu-title="One" -->
# Lessons for kernel

* Tiny VMs matter
  - 1 vCPU VMs with 200MB RAM (or less)
  - can smoothly scale horizontally
  - better for HA
  - can start VMs close to users
  - 32-bit still makes sense
  - Even big central DB can be sharded
* Boot time matters
* Unikernels are cool
