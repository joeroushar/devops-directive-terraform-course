# DevOps Directive Terraform Course

This is the companion repo to: [Complete Terraform Course - From BEGINNER to PRO! (Learn Infrastructure as Code)](https://www.youtube.com/watch?v=7xngnjfIlK4)

[![thumbnail](https://user-images.githubusercontent.com/1320389/154354937-98533608-2f42-44c1-8110-87f7e3f45085.jpeg)](https://www.youtube.com/watch?v=7xngnjfIlK4)

## 01 - Evolution of Cloud + Infrastructure as Code

High level overview of the evolution of cloud computing and infrastructure as code.

This module does not have any corresponding code.

## 02 - Overview + Setup

Terraform overview and setup instructions.

Includes basic `hello world` terraform config to provision a single AWS EC2 instance.

## 03 - Basics

Covers main usage pattern, setting up remote backends (where the terraform state is stored) using terraform Cloud and AWS, and provides a naive implementation of a web application architecture.

## 04 - Variables and Outputs

Introduces the concepts of variables which enable Terraform configurations to be flexible and composable. Refactors web application to use these features.

## 05 - Language Features

Describes additional features of the Hashicorp Configuration Language (HCL).

## 06 - Organization and Modules

Demonstrates how to structure terraform code into reuseable modules and how to instantiate/configure modules.

## 07 - Managing Multiple Environments

Shows two methods for managing multiple environments (e.g. dev/staging/prodution) with Terraform.

## 08 - Testing

Explains different types of testing (manual + automated) for Terraform modules and configurations.

## 09 - Developer Workflows + CI/CD

Covers how teams can work together with Terraform and how to set up CI/CD pipelines to keep infrastructure environments up to date.

for **Reverse Engineering**: https://medium.com/@xpiotrkleban/reverse-engineering-existing-infrastructure-to-terraform-hcl-files-453420059ef9 
https://medium.com/developing-koan/from-pebbles-to-brickworks-a-story-of-cloud-infrastructure-evolved-1c9ca5495cdc

Terraform only tracks in its state the objects that it created, or that it was explicitly asked to “adopt” using the import features. If anyone has created anything entirely new since the last use of Terraform then Terraform will have no awareness of it at all, and so won’t know to track it unless you explicitly import it.

You mentioned switching wholesale from directly-managed nodes to EKS managed node groups, which I guess means that your most recent Terraform state will be tracking a bunch of individual EC2 instances, or an autoscaling group, or similar, and crucially it won’t have any knowledge about your newer EKS node groups.

With that all considered, I would guess that trying to describe the current system that exists using a fresh Terraform configuration and then importing your existing infrastructure into it would probably be the shortest and least fraught path here, because:

you can presumably inspect the current situation in the running system to reverse-engineer any details you aren’t sure about, whereas the old system that you existing Terraform configuration was written for essentially no longer exists and so you’d need to rely on institutional memory to understand how it was designed and why.
starting from a fresh Terraform state and gradually importing things into it means you can approach this task gradually with less risk to the running system; you can hopefully adopt one layer into Terraform at a time, reach the point where it plans “no changes” (meaning the desired state and actual state are converged), and then move on to the next layer. (I’m using “layer” in a general sense here; you will need to decide what makes sense as cut points based on how the system is designed, but the idea would be to at each point have as clear as possible a line between what is currently Terraform-managed and what isn’t, so you can communicate with the rest of your team and make sure coworkers aren’t effectively undoing your work by making even more changes outside of Terraform.)
You presumably now have the experience of running this real system and know what sorts of changes are likely to happen routinely and what sorts of changes are less common. That is likely to influence how you design your Terraform configuration, and trying to bring forward design decisions from the earlier phase of the system may force you down a less useful design path as you try to keep the old Terraform state matching when you update things.
