# home-lab
This repository is focused on collecting all of the artifacts that will go into building out my new home lab.  This lab will be mainly for learning and practicing different technologies, but will also try to serve for creating useful tools that I may use in my personal and professional daily life.

I will make heavy use of AI to help answer questions that I have along the way, and try to document both positive and negative experiences along the way.


Update on 4/13/2024:

I have leared a lot in this first month and a half.  I have gotten a lot of hands on experience and have made a number of mistakes along the way but have probably learned more by having to fix those.  The K8s installation has proven to be challenging and I have probably spent more time trying to install items here than with a normal installation, and much more than just spinning up a docker image.  However, this whole process is about learning and getting some practical exerience, and not the on the specific applications that I will be running here.  I have done a mix of installation types.  Although I want my application deployments to be pure GitOps with Flux/Kustomization, I was open to trying different ways for doing my infrastructure.  A lot of infrastructure was done via helm, but typically with a values.yaml for specific configuration.  At this point, I have a fairly robust infratructure with the following:

<ul>
<li> NFS storage to my Synology NAS
<li> ElasticSearch
<li> PostgreSQL
<li> Splunk
<li> Kafka with Kafka-UI
<li> Prometheus
<li> Tempo
<li> Grafana
<li> OpenTelementry Collector
<li> MetalLB as the load balancer
</ul>

One of my biggest challenges was around setting up MetalLB and Ingress.  The root cause of my issues was a silly mistake on using the IP range of my cluster instead of a dedicated range of free IPs.  I had just jumped right in on the installation without reading enough to understand it.  I also think trying to do Ingress and MetalLB at the same time was a mistake.  In trying to troubleshoot, I actually changed so many items, that I couldn't even keep up with them.  I ended up restoring from backups and continued on.  I now have MetalLB working perfectly but my current use case does not require Ingress, so I will tackle that at a later date.


What am I running?  At this point, the only application I am running is a basic Python Weather Application that calls an external API and logs the data to the PostgreSQL database.  I have started with the basics of OpenTelemetry and want to spend more time in this area.  At this point, I do have the logs going to Splunk via the OpenTelementry collector,  Traces going to Tempo, and Metrics going to Prometheus.  I have the Traces and Metrics being displayed in Grafana along with an application specific Dashboard.  I cannot display the Splunk logs in Grafana due to not having the enterprise licenses, so I just view those in Splunk.  I will switch things around as I go and try full visibility in ElasticSearch or full visibility in Splunk to get some hands on exeriences with their observability tools.  I will clean up my weather application along the way and create a few more applications, but again, the main purpose is about the learning.

<hr/>

## Initial Setup

### Goals
<ul>
<li> Primary Lab will consist of 3 Kubernetes nodes - 1 as the control plane and 2 work nodes
<li> Use Ansible to create playbooks to setup and backup the environment
<li> Experiment with local LLM that will be hosted on a separate machine
<li> Keep the cost very low as this is just for playing with and will just be for personal use
</ul>

### Hardware
<ul>
<li> 3 renewed Lenovo ThinkCentre M700 - $110 each
    <ul>
    <li> 1 - control plane
    <li> 2 - worker nodes
    </ul>
<li> Mac Mini Desktop Computer with M4 chip with 10‑core CPU and 10‑core GPU - $550 (existing)
    <ul>
    <li> Main development machine
    <li> Host local LLM for experimental purposes
    </ul>
</ul>

### Thoughts and Decisions
<ul>
<li> Cost was one of the main deciding factor, but still factor in the other needs
<li> Wanted to use similar hardware across all so that I did not need to fight individual issues with repurposed machines that I had lying around.
<li> Wanted to be able to expand or replace machines with little investment
<li> Wanted machines that were very power efficient as they will be running 24x7, but most of the time they will be idle.
<li> Wanted to work with some LLM but hardware to run a decent size model and get good token per second rate is just too expensive.  I have therefore decided to work in a hybrid model of having some LLM for training and other experimenting, however will rely more on public models for most of the duties.  API usage cost will be a fraction of what it would cost to host a decent model
</ul>


