This document aims to provide a possible way of how Azure infrastructure changes can be deployed in the future.

The changes, which are discussed in this document, are mainly those in the [jore4-deploy](https://github.com/HSLdevcom/jore4-deploy) repo.

Basic development process
-------------------------

1. Infrastructure development and try-outs should be performed in the `playg` resource group.
1. After a PR has been merged into the `main` branch, the changes should be applied to the `dev` resource group.
1. After this step, the changes should be deployed in the `test` and `prod` resource groups in connection with planned releases in these environments.
   This might require release planning in order not to cause conflicts between functional components and the infrastructure they run in.
1. At regular intervals, the whole infrastructure should be rebuilt from scratch, because an update can behave quite differently than a create operation
   with the tools used. The regular rebuilding can be skipped if no changes have been made since the last rebuilding.

Open issues
-----------
- how to communicate upcoming infrastructure changes to the person performing the release? ("release planning")
  - document containing check-list / steps of upcoming releases?
- at which interval should the infrastructure be recreated if changes have occurred
  - monthly?
- environment in which the recreation should be performed
  - could the `playg` environment be used or should there be a separate one?
