---
menu:
  docs:
    parent: apps
title: GovCloud guide
---

cloud.gov currently has two "environments" where orgs can live: the main environment that most orgs are using (located in AWS East/West) and a new GovCloud environment (located in [AWS GovCloud](https://aws.amazon.com/govcloud-us/)).

When your org starts using cloud.gov's GovCloud environment, here are changes to look out for.

### Breaking changes

- GSA and EPA accounts already work in GovCloud. No other login credentials work at this time.
- The API endpoint (for now) is `api.fr.cloud.gov`. When you [log in on the command line]({{< relref "docs/getting-started/setup.md" >}}), use this new command: `cf login -a https://api.fr.cloud.gov --sso`
- To access the Dashboard, visit [`https://dashboard.fr.cloud.gov/`](https://dashboard.fr.cloud.gov/).
- There are different names and plans for some services.

    | AWS East/West service name | AWS GovCloud equivalent |
    | --- | --- |
    | `elasticsearch-swarm-*` | `elasticsearch*` |
    | `rds` | `aws-rds` |
    | `redis*-swarm` | `redis*` |

- Elasticsearch service instances no longer provide a `uri` property under the `credentials`.
    - The URL needs to be constructed manually (with a protocol of `http://`), or the URL components need to be passed to your driver directly. The components are:
        - `username`
        - `password`
        - `hostname`
        - `port`

### New features

- To run [one-off tasks]({{< relref "docs/getting-started/one-off-tasks.md" >}}), [`cf-ssh`]({{< relref "docs/apps/using-ssh.md#east-west-environment-cf-ssh" >}}) is not available in GovCloud. Instead, you can use [`cf ssh`]({{< relref "docs/apps/using-ssh.md#govcloud-environment-cf-ssh" >}}) (note the space instead of the `-`), which is more flexible than `cf-ssh`.
- To set up custom domains, you can use the [managed service method]({{< relref "docs/apps/custom-domains.md#managed-service-method" >}}).

#### Experimental new features

- You can [require cloud.gov authentication to access your application]({{< relref "docs/apps/experimental/experimental-services.md#authorization-proxy" >}}).
- You can [push Docker images]({{< relref "docs/apps/experimental/docker.md" >}}).

### Tips

- If you need to work in both the East/West and GovCloud environments, [this Cloud Foundry plugin](https://github.com/guidowb/cf-targets-plugin) may be helpful.

### Migration

_This section is a work in progress._

As part of [cloud.gov achieving FedRAMP authorization](https://18f.gsa.gov/2016/07/18/cloud-gov-full-steam-ahead-fedramp-assessment-process/), all tenants in the AWS East/West will need to move to the new GovCloud environment.

#### Who this affects

Any systems running in the AWS East/West version of cloud.gov. To find all of the apps you have access to, visit https://dashboard.cloud.gov. All apps that aren't moved by [TODO] will be deleted, so make sure that you or someone on your team [coordinates]({{< relref "docs/help.md" >}}) with the cloud.gov team to ensure your migration is completed as smoothly as possible.

#### Costs

For [resource usage]({{< relref "overview/pricing/rates.md" >}}), we will bill East/West usage until your migration is complete, then switch to billing GovCloud usage. In other words, you will not be double-charged while migrating.

The cloud.gov team estimates that **migrating a single "system" should take between one hour and one week** of developer time, depending on:

* For the developer(s) handling the move:
    * Level of cloud.gov experience
    * Familiarity with the architecture of the system
* Complexity of the architecture
* Whether the system is already following the [deployment best practices]({{< relref "docs/apps/production-ready.md" >}})
* If the system is using one or more databases/[services]({{< relref "docs/apps/managed-services.md" >}})
* The [availability](https://simple.wikipedia.org/wiki/Availability) requirements of your system
    * How big of a deal is it if your system goes down for a few minutes? or an hour or more?

Any labor involved from your project team is the responsibility of your project. There is no additional charge for support from the cloud.gov team assisting your developers with the migration.

#### Process

1. Ensure that a organization is created for your system in GovCloud. [TODO explain how to do this]
1. Install the [CF Targets plugin](https://github.com/guidowb/cf-targets-plugin).
1. [Set up the spaces.]({{< relref "docs/apps/production-ready.md#space-per-environment" >}})
1. [Give permissions to the appropriate people.]({{< relref "docs/apps/managing-teammates.md" >}})
1. Deploy the application to the GovCloud environment.
    * This is a good time to ensure that you are following [deployment best practices]({{< relref "docs/apps/production-ready.md" >}}).
1. If you are using a data store provided by cloud.gov, [migrate the data](https://github.com/18F/cg-product/issues/211). This includes the following [services]({{< relref "docs/apps/managed-services.md" >}}):
    * [Elasticsearch](https://github.com/18F/cg-product/issues/233)
    * [MySQL](https://github.com/18F/cg-product/issues/231)
    * [PostgreSQL](https://github.com/18F/cg-product/issues/230)
    * [Redis](https://github.com/18F/cg-product/issues/234)
    * [S3](https://github.com/18F/cg-product/issues/235)
1. Test the new deployment(s) thoroughly.
1. [Set up your custom domain]({{< relref "docs/apps/custom-domains.md#managed-service-method" >}}), if applicable.
1. [Set up continuous deployment.]({{< relref "docs/apps/continuous-deployment.md" >}}) (Not required, but strongly recommended.)
1. Once you are confident that the move is complete, [ask support]({{< relref "docs/help.md" >}}) to delete your old organization.

#### Unresolved details

The cloud.gov team is still working through details on the following:

* More explicit instructions for the steps above
* Timeline/[deadlines](https://github.com/18F/cg-product/issues/403) for completing the migration
* The support arrangement that the cloud.gov team will provide for migration assistance

All affected tenants will be notified as more information is available.
