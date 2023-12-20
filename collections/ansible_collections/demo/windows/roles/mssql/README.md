mssql
=========

This role installs PowerShell requirements and adds Windows features required  for SQL Server before completing an installation of a SQL Server instance using  desired state configuration.

Minimum Ansible Version: 2.1

Galaxy Tags: \[ mssql dsc windows \]

Role Variables
--------------

| Variable | Default | Value or Expression |
| -------- | ------- | ------------------- |
| mssql_sqlsvc_account_pass | ❌ | _REQUIRED_ |
| mssql_agentsvc_account_pass | ❌ | _REQUIRED_ |
| mssql_assvc_account_pass | ❌ | _REQUIRED_ |
| mssql_version | ✅ | 2022 |
| mssql_version_media | ✅ | {'2022': ... |
| mssql_installation_source | ✅ | {{ mssql_version_media[mssql_version] }} |
| mssql_installation_path | ✅ | C:\SQLInstall |
| mssql_temp_download_path | ✅ | C:\tmp |
| mssql_instance_name | ✅ | MSSQLSERVER |
| mssql_drive | ✅ | C |
| mssql_userdbvol_name | ✅ | Userdbvol01 |
| mssql_port | ✅ | 1433 |
| mssql_max_server_memory | ✅ | 1024 |
| mssql_os_memory_reservation | ✅ | 512 |
| mssql_total_system_memory | ✅ | {{ mssql_max_server_memory + ... |
| mssql_suppress_reboot | ✅ | False |
| mssql_base_ldap_path | ✅ | ou=Users,dc=contoso,dc=com |
| domain_controller | ✅ | dc01 |
| netbios | ✅ | CONTOSO |
| mssql_sqlsvc_account | ✅ | {{ netbios }}\sql_svc |
| mssql_agentsvc_account | ✅ | {{ netbios }}\sql_agt |
| mssql_assvc_account | ✅ | {{ mssql_sqlsvc_account }} |
| mssql_userdbvol_path | ✅ | {{ mssql_drive }}:\{{ mssql_userdbvol_name }} |
| mssql_db_accesspath | ✅ | {{ mssql_userdbvol_path }}\DatabaseFiles |
| mssql_logs_accesspath | ✅ | {{ mssql_userdbvol_path }}\DatabaseLogs |
| mssql_installshared_path | ✅ | C:\Program Files\Microsoft SQL Server |
| mssql_installsharedwow_path | ✅ | C:\Program Files (x86)\Microsoft SQL Server |
| mssql_instance_path | ✅ | C:\Program Files\Microsoft SQL Server\{{ ... |
| mssql_sqlinstalldata_path | ✅ | {{ mssql_db_accesspath }}\{{mssql_instance_name }} |
| mssql_sqluserdata_path | ✅ | {{ mssql_db_accesspath }}\{{mssql_instance_name }} |
| mssql_sqluserlog_path | ✅ | {{ mssql_logs_accesspath }}\{{mssql_instance_name }} |
| mssql_sqltempDB_path | ✅ | C:\TempDBFiles\Data\{{mssql_instance_name }} |
| mssql_sqltempDBlog_path | ✅ | C:\TempDBFiles\Log\{{mssql_instance_name }} |
| mssql_security_mode | ✅ | sql |
| mssql_sa_password | ✅ | {{ mssql_sqlsvc_account_pass }} |
| mssql_features | ✅ | SQLENGINE,AS |
| mssql_collation | ✅ | SQL_Latin1_General_CP1_CI_AS |
| mssql_browsersvc_mode | ✅ | Automatic |
| mssql_sysadmin_accounts | ✅ | ['AUTODOTES\\Domain Admins', ... |
| mssql_asadmin_accounts | ✅ | {{ mssql_sysadmin_accounts }} |
| mssql_max_degree_of_parallelism | ✅ | 0 |
| mssql_min_server_memory | ✅ | 0 |
| mssql_profiles_allowed | ✅ | ['Private', 'Domain', 'Public'] |

Handlers
--------------

  - reboot windows
  - restart sqlagent

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

  ```yaml
    - name: Install MSSQL using DSC
      ansible.builtin.include_role:
        name: mgmt.windows.mssql
      vars:
        netbios: ANSIBLE
        mssql_base_ldap_path: "OU=Users,DC=ansible,DN=local"

        ### From custom credential ###
        # mssql_sqlsvc_account_pass:
        # mssql_agentsvc_account_pass
        # mssql_assvc_account_pass:
  ```

License
-------

license (GPL-2.0-or-later, MIT, etc)

Author Information
-------
**Zach LeBlanc**

Adapted from @kkolk/mssql