# home-lab
This repository is focused on collecting all of the artifacts that will go into building out my new home lab.  This lab will be mainly for learning and practicing different technologies, but will also try to serve for creating useful tools that I may use in my personal and professional daily life.

I will make heavy use of AI to help answer questions that I have along the way, and try to document both positive and negative experiences along the way.
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

My thought process for this was based upon a back and forth discussion with Grok 3 to weigh various pros and cons of the setup.  Details of the conversation can be found at:  https://x.com/i/grok/share/YmrGTiwFItV9iXhCGS4AzM5RH


