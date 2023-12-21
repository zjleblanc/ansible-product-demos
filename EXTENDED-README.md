# extended workshop

## setup

1. Set Project Details
    - source control url: https://github.com/zjleblanc/ansible-product-demos.git
    - branch: xom/windows-workshop
2. Setup Automation Hub credential
3. Run the setup job template for windows and cloud
4. Setup the AWS credential
5. Use postman to create windows vms from `windows_full` blueprint
     - one for domain controller
     - x additional vms for demo
     - ec2-user in Administrators group with admin password same as AAP
6. Run the Create AD Domain job (wait for vm to spin up if you get connection errors)
7. Download RDP for domain controller and open Server Manager to support demos
8. Add rules to security group for SQL Server
    - TCP 1433 for 0.0.0.0 (SQL Server)
    - UDP 1434 for 0.0.0.0 (SQL Server Browser)
    - Supports showing connection from DBeaver

## demos

- Run the **Windows / AD / Join Domain** job against all additional vms
  - Re-run the Join Domain job against all additional vms to demonstrate idempotency (optional)
- Run the **Windows / Patching** job
  - specify one of the windows vms as the `report_server`
  - select `DefinitionUpdates` as a category
  - there likely won't be many updates as the VM was just created
  - navigate to the IIS server report (optionally show IIS setup)
- Run **Windows / Chocolatey install multiple** against whichever vms
  - show packages added [here](./windows/windows_choco_multiple.yml)
- Run **Windows / Configuring Password Requirements**
  - **before** run `net accounts` on the target host
  - show password config changes [here](./windows/powershell_dsc.yml)
  - - **before** run `net accounts` again on the target host to show application of policy
- Install SQL Server
  - Create the SQL Server Credentials **Credential Type**
  - Create a credential using ANSIBLE\ for account name prefixes
  - Create Windows \ Install SQL Server job template
    - attach Demo Credential
    - attach SQL Server Credential
    - create survey
      - target hosts pattern
      - domain controller inventory hostname
