# Service software updates in Amazon OpenSearch Service<a name="service-software"></a>

**Note**  
Service software updates differ from OpenSearch version upgrades\. For information about upgrading to a later version of OpenSearch, see [Upgrading Amazon OpenSearch Service domains](version-migration.md)\.

Amazon OpenSearch Service regularly releases system software updates that add features or otherwise improve your domains\. The **Notifications** panel in the console is the easiest way to see if an update is available or check the status of an update\. Each notification includes details about the service software update\. The notification severity is `Informational` if the update is optional and `High` if it's required\. For more information about notifications, see [Notifications in Amazon OpenSearch Service](managedomains-notifications.md)\. 

## Domain update considerations<a name="service-software-considerations"></a>

Consider the following when deciding whether to update your domain:
+ If you take no action on required updates, OpenSearch Service still updates your domain service software automatically after a certain timeframe \(typically two weeks\)\. In this situation, OpenSearch Service sends notifications when it starts the update and when the update is complete\.
+ If you start an update manually, OpenSearch Service doesn't send a notification when it starts the update, only when the update is complete\.
+ Software updates use [blue/green deployments](managedomains-configuration-changes.md) to minimizes downtime\. Updates can temporarily strain a cluster's dedicated master nodes, so make sure to maintain sufficient capacity to handle the associated overhead\. 

Manually updating your domain lets you take advantage of new features more quickly\. When you choose **Update**, OpenSearch Service places the request in a queue and begins the update when it has time\. Updates typically complete within minutes, but can also take several hours or even days if your system is experiencing heavy load\. Consider updating your domain at a low traffic time to avoid long update periods\. 

## Patch releases<a name="service-software-patches"></a>

Service software versions that end in "\-P" and a number, such as R20211203\-*P4*, are patch releases\. Patches are likely to include performance improvements, minor bug fixes, and security fixes or posture improvements\. Patch releases do not include new features or breaking changes, and they generally do not have a direct or noticeable impact on users\.

## Request a service software update<a name="service-software-request"></a>

**To request a service software update \(console\)**

1. Go to [https://aws\.amazon\.com](https://aws.amazon.com) and choose **Sign In to the Console**\.

1. Under **Analytics**, choose **Amazon OpenSearch Service**\.

1. In the navigation pane, under **Domains**, choose the domain name to open its settings\.

1. Choose **Actions**, **Update** and confirm the update\.

**To request a service software update \(AWS CLI and AWS SDKs\)**

You can use the following commands to see if an update is available, check upgrade eligibility, and request an update:
+ `describe-domain` \(`DescribeDomain`\)
+ `start-service-software-update` \(`StartServiceSoftwareUpdate`\)

For more information, see the [AWS CLI command reference](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/opensearch/index.html) and [Configuration API reference for Amazon OpenSearch Service](configuration-api.md)\.

**Tip**  
After requesting an update, you might have a narrow window of time in which you can cancel it\. The duration of this `PENDING_UPDATE` state can vary greatly and depends on your AWS Region and the number of concurrent updates OpenSearch Service is performing\. To cancel, use the console or `cancel-service-software-update` \(`CancelServiceSoftwareUpdate`\) command\.

## Monitoring service software update events<a name="service-software-monitor"></a>

OpenSearch Service sends a [notification](managedomains-notifications.md) when a service software update is available, required, started, completed, or failed\. You can view these notifications on the **Notifications** panel of the OpenSearch Service console\. It also sends these notifications as events to Amazon EventBridge, where you can configure rules that send an email or perform a specific action when an event is received\. For an example walkthrough, see [Tutorial: Sending Amazon SNS alerts for available software updates](sns-events.md)\.

For the format of each service software event sent to Amazon EventBridge, see [Service software update events](monitoring-events.md#monitoring-events-sso)\.

## When domains are ineligible for an update<a name="service-software-ineligible"></a>

Your domain is ineligible for a service software update if it's in any of the following states:


| State | Description | 
| --- | --- | 
| Domain in processing |  The domain is in the middle of a configuration change\. Check update eligibility after the operation completes\.  | 
| Red cluster status |  One or more indices in the cluster is red\. For troubleshooting steps, see [Red cluster status](handling-errors.md#handling-errors-red-cluster-status)\.  | 
| High error rate |  The OpenSearch cluster is returning a large number of 5*xx* errors when attempting to process requests\. This problem is usually the result of too many simultaneous read or write requests\. Consider reducing traffic to the cluster or scaling your domain\.  | 
| Split brain |  *Split brain* means your OpenSearch cluster has more than one master node and has split into two clusters that never will rejoin on their own\. You can avoid split brain by using the recommended number of [dedicated master nodes](managedomains-dedicatedmasternodes.md)\. For help recovering from split brain, contact [AWS Support](https://console.aws.amazon.com/support/home)\.  | 
| Amazon Cognito integration issue |  Your domain uses [authentication for OpenSearch Dashboards](cognito-auth.md), and OpenSearch Service can't find one or more Amazon Cognito resources\. This problem usually occurs if the Amazon Cognito user pool is missing\. To correct the issue, recreate the missing resource and configure the OpenSearch Service domain to use it\.  | 
| Other OpenSearch Service service issue |  Issues with OpenSearch Service itself might cause your domain to display as ineligible for an update\. If none of the previous conditions apply to your domain and the problem persists for more than a day, contact [AWS Support](https://console.aws.amazon.com/support/home)\.  | 