---
layout: post
title:  "Implement Devops with IBM TAAS"
categories: Devops
tags:  Jenkins Artifactory
excerpt: null
mathjax: true
author: Bo Chen
---

* content
{:toc}

# What is IBM TAAS

## URL

[https://taas-home.w3ibm.mybluemix.net/](https://taas-home.w3ibm.mybluemix.net/)

## Our Mission

A centralized DevOps team based in Watson & Cloud Platform that provides development teams across IBM with the best industry standard development tools hosted at 99.99% availability along with documented tooling best practices and The DevOps Playbook.

## Benefits

We remove hosting responsibilities from development teams. Teams receive guidance on the most efficient and speedy way to developing, building, and deploying cloud native services. Development teams can focus on delivery and support.

## Location

Today all of the tools listed above are being hosted in a Bluemix (formerly Softlayer) environment with full connectivity to the IBM Network over dedicated tunnels. We are doing this with a full team effort between the TaaS team and IBM site network teams with many of the best practices you will see in the Tools Guide.

# Onboard process and access control

## Jenkins

### Overview

Jenkins is a continuous integration and continuous delivery application. By continuously building and testing software projects with Jenkins, it becomes easier for developers to integrate changes in a project and to find and solve defects more rapidly, thereby increasing your productivity.

### Onboarding

To onboard onto Jenkins please use the link [TaaS Onboarding App](http://taas-box.rtp.raleigh.ibm.com/#/) and select Jenkins, and fill out the proper details.

If you need assistance or run into problems with our Onboarding Application, please reach out to us in Slack #taas-jenkins-help we would love to help you.

### Accessing

You can access your Jenkins Master at: https://gbs-micros-jenkins.swg-devops.com

Two bluegroups have been automatically created for you to control access to Jenkins:
Admin access: [jaas-sys-core-services-admin](https://w3.ibm.com/tools/groups/protect/groupsgui.wss?task=ViewGroup&gName=jaas-gbs-micros-admin&showlist=true)
Developer access: [jaas-sys-core-services-dev](https://w3.ibm.com/tools/groups/protect/groupsgui.wss?task=ViewGroup&gName=jaas-gbs-micros-dev&showlist=true)

You can find [additional documentation](https://pages.github.ibm.com/TAAS/tools_guide/jenkins/getting-started.html) for getting started @ 

You can open user requests at [https://pages.github.ibm.com/TAAS/tools_guide/support.html](https://pages.github.ibm.com/TAAS/tools_guide/support.html)
You can also ask questions to the community in the Enterprise Slack channel #taas-jenkins-help

## Artifactory

### Overview

Artifactory is the first, and only, universal Artifact Repository Manager on the market, JFrog Artifactory fully supports software packages created by any language or technology. Artifactory is the only enterprise-ready repository manager available today, supporting secure, clustered, High Availability Docker registries. Integrating with all major CI/CD and DevOps tools, Artifactory provides an end-to-end, automated and bullet-proof solution for tracking artifacts from development to production.

### Onboarding

To onboard onto Artifactory please use the link [TaaS Onboarding App](http://taas-box.rtp.raleigh.ibm.com/#/) and select Artifactory, and fill out the proper details.

Modifying existing repositories is currently not supported yet. Please Open a Ticket to perform this tasks.

If you need assistance or run into problems with our Onboarding Application, please reach out to us in Slack #taas-artifactory-help we would love to help you.

### Accessing

Details can be found here: [https://github.ibm.com/Cloud-DevOps-Transformation-Services/artifactory-repositories/pull/1095](https://github.ibm.com/Cloud-DevOps-Transformation-Services/artifactory-repositories/pull/1095)

You can access your repositories via the primary server: [https://eu.artifactory.swg-devops.com/artifactory](https://eu.artifactory.swg-devops.com/artifactory)
In addition, your repositories are replicated secondary, read-only, server(s): [https://na.artifactory.swg-devops.com/artifactory](https://na.artifactory.swg-devops.com/artifactory)

The following repositories have been configured:
  gbs-micros-docker-local  [docker repo @ gbs-micros-docker-local.artifactory.swg-devops.com]
  gbs-micros-npm-local  [npm repo]
  gbs-micros-maven-local  [maven repo]

The following bluegroups have been automatically created for you to manage access for your team members:
afaas-gbs-micros-write > [https://w3.ibm.com/tools/groups/protect/groupsgui.wss?task=ViewGroup&gName=afaas-gbs-micros-write&showlist=true](https://w3.ibm.com/tools/groups/protect/groupsgui.wss?task=ViewGroup&gName=afaas-gbs-micros-write&showlist=true)
afaas-gbs-micros-read > [https://w3.ibm.com/tools/groups/protect/groupsgui.wss?task=ViewGroup&gName=afaas-gbs-micros-read&showlist=true](https://w3.ibm.com/tools/groups/protect/groupsgui.wss?task=ViewGroup&gName=afaas-gbs-micros-read&showlist=true)

The admins who have permission to modify these groups are:
bchen@cn.ibm.com

You can find additional documentation for getting started @ [https://pages.github.ibm.com/TAAS/tools_guide/artifactory/getting-started.html](https://pages.github.ibm.com/TAAS/tools_guide/artifactory/getting-started.html)

You can open user requests at [https://pages.github.ibm.com/TAAS/tools_guide/support.html](https://pages.github.ibm.com/TAAS/tools_guide/support.html)
You can also ask questions to the community in the Enterprise Slack channel #taas-artifactory-help

## UrbanCode Deploy

### Overview

IBM UrbanCode Deploy (UCD) is a tool for automating application deployments through your environments. It is designed to facilitate rapid feedback and continuous delivery in agile development while providing the audit trails, versioning and approvals needed in production.

### Onboarding

To onboard onto UrbanCode Deploy please use the link [TaaS Onboarding App](http://taas-box.rtp.raleigh.ibm.com/#/) and select UrbanCode Deploy, and fill out the proper details.

If you need assistance or run into problems with our Onboarding Application, please reach out to us in Slack #taas-ucd-help we would love to help you.

### Accessing

Your team: Composable Architecture has been onboarded to UCD!

You can access your team at: [https://ucdeploy2.swg-devops.com](https://ucdeploy2.swg-devops.com)

Your Team Admin(s) can add other developers/team-admins through the UCD Web UI

You can find additional documentation for getting started @ [https://pages.github.ibm.com/TAAS/tools_guide/ucd/getting-started.html](https://pages.github.ibm.com/TAAS/tools_guide/ucd/getting-started.html)

You can open user requests at [https://pages.github.ibm.com/TAAS/tools_guide/support.html](https://pages.github.ibm.com/TAAS/tools_guide/support.html)
You can also ask questions to the community in the Enterprise Slack channel #taas-ucd-help