# CloudFormation OpenSearch upgrade issue

This reproduces an odd issue with CloudFormation upgrading OpenSearch engine versions.

1. Deploy the [`00-initial.yml`](00-initial.yml) template as a new stack.
2. Try to update the stack with the [`02-final.yml`](02-final.yml) template. This fails as you cannot directly upgrade an OpenSearch domain from Elasticsearch 7.10 to OpenSearch 2.19.
3. Upgrade the domain manually using the OpenSearch console - first to OpenSearch 1.3 and then to OpenSearch 2.19.
4. Try again to update the stack with the [`02-final.yml`](02-final.yml) template. This fails with an Internal Failure. In CloudTrail we can see an `UpgradeDomain` event at this time with an `errorMessage` `Failed to submit upgrade. Upgrade from OpenSearch_2.19 to OpenSearch_2.19 not supported.`
    * The stack is now in `UPDATE_ROLLBACK_FAILED`, because the rollback tried to downgrade the cluster back to Elasticsearch 7.10. Continue the rollback, skipping the OpenSearch domain, to get the stack back to a usable state.
5. Update the stack with the [`01-intermediate.yml`](01-intermediate.yml) template.
    * In this repro, this fails in the same way as step 4.
    * The equivalent step succeeded in our actual production stack, even though we still could see a related `UpgradeDomain` event in CloudTrail with the same error message.
6. Update the stack with the [`02-final.yml`](02-final.yml) template.
    * Again, this worked in our production environment, but I could not get this far with this reproduction.
