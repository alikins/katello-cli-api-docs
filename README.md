
katello-cli-tests-1.3.6-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index 533a822..2f422d3 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -13,11 +13,10 @@ CLASSES

         activation_key(self, orgId, keyId)

-        create(self, envId, name, description, usage_limit=-1,
-               templateId=None)
+        create(self, envId, name, description, usage_limit=-1, view_id=None)

         update(self, orgId, keyId, environmentId, name, description,
-               templateId, usage_limit)
+               usage_limit, view_id)

         add_pool(self, orgId, keyId, poolid)

diff --git a/katello.client.api.filter b/katello.client.api.filter
index 58ca285..ffd793e 100644
--- a/katello.client.api.filter
+++ b/katello.client.api.filter
@@ -5,17 +5,21 @@ MODULE NAME
 CLASSES

     class FilterAPI(katello.client.api.base.KatelloAPI)
-        Connection class to access Filter Data
+        Connection class to access content view filter calls


-        filters(self, org)
+        filters_by_cvd_and_org(self, def_id, org_id)

-        create(self, org, name, description, filter_list)
+        get_filter_info(self, filter_name, def_id, org_id)

-        update(self, org, filter_id, new_name)
+        create(self, filter_name, def_id, org_id)

-        delete(self, org, name)
+        delete(self, filter_name, def_id, org_id)

-        info(self, org, name)
+        products(self, filter_name, def_id, org_id)

-        update_packages(self, org, name, package_list)
+        update_products(self, filter_name, def_id, org_id, products)
+
+        repos(self, filter_name, def_id, org_id)
+
+        update_repos(self, filter_name, def_id, org_id, repos)
diff --git a/katello.client.api.product b/katello.client.api.product
index 6e17144..a7a2c7a 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -35,3 +35,9 @@ CLASSES
         cancel_sync(self, orgName, prodId)

         last_sync_status(self, orgName, prodId)
+
+        repository_sets(self, orgName, prodId)
+
+        enable_repository_set(self, orgName, prodId, repoSetId)
+
+        disable_repository_set(self, orgName, prodId, repoSetId)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index b5c4c91..18d37f5 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,7 +8,8 @@ CLASSES
         Connection class to access repositories


-        create(self, orgName, prod_id, name, label, url, gpgkey, nogpgkey)
+        create(self, orgName, prod_id, name, label, url, unprotected,
+               gpgkey, nogpgkey)

         update(self, repo_id, gpgkey, nogpgkey)

diff --git a/katello.client.api.system b/katello.client.api.system
index 688e3cf..f0d21cc 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -9,7 +9,7 @@ CLASSES


         register(self, name, org, environment_id, activation_keys, cp_type,
-                 release=None, sla=None, facts=None)
+                 release=None, sla=None, facts=None, view_id=None)

         unregister(self, system_uuid)

diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index a9dd56d..ddfed0f 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -47,3 +47,5 @@ CLASSES
         errata(self, org_id, system_group_id, type_in=None)

         install_errata(self, org_id, system_group_id, errata)
+
+        update_systems(self, org_id, system_group_id, env_id, view_id)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 7a24b35..a1bce7f 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -41,12 +41,15 @@ FUNCTIONS
     get_product(orgName, prodName=None, prodLabel=None, prodId=None)
         Retrieve product by name, label or id.

-    get_repo(orgName, prodName, prodLabel, prodId, repoName, envName=None,
-             includeDisabled=False)
+    get_content_view(org_name, view_label=None, view_name=None,
+                     view_id=None)

-    get_provider(orgName, provName)
+    get_cv_definition(org_name, def_label=None, def_name=None, def_id=None)
+
+    get_repo(orgName, repoName, prodName=None, prodLabel=None, prodId=None,
+             envName=None, includeDisabled=False)

-    get_template(orgName, envName, tplName)
+    get_provider(orgName, provName)

     get_changeset(orgName, envName, csName)

@@ -61,3 +64,5 @@ FUNCTIONS
     get_system_group(org_name, system_group_name)

     get_system(org_name, sys_name, env_name=None, sys_uuid=None)
+
+    get_distributor(org_name, dist_name, env_name=None, dist_uuid=None)


```

katello-cli-tests-1.3.5-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index 7bd14e6..6e17144 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -35,7 +35,3 @@ CLASSES
         cancel_sync(self, orgName, prodId)

         last_sync_status(self, orgName, prodId)
-
-        update_filters(self, orgName, prodId, filters)
-
-        filters(self, orgName, prodId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index 9db8ecf..67151c6 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -31,3 +31,5 @@ CLASSES
         delete_manifest(self, provId)

         refresh_products(self, provId)
+
+        repo_discovery(self, provId, url)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 579c917..b5c4c91 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -31,10 +31,6 @@ CLASSES

         last_sync_status(self, repo_id)

-        repo_discovery(self, org_name, url, repotype)
-
-        repo_discovery_status(self, discoveryTaskId)
-
         packagegroups(self, repoid)

         packagegroup_by_id(self, repoid, groupId)
@@ -42,7 +38,3 @@ CLASSES
         packagegroupcategories(self, repoid)

         packagegroupcategory_by_id(self, repoid, categoryId)
-
-        update_filters(self, repo_id, filters)
-
-        filters(self, repo_id, inherit=False)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 786e104..7a24b35 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -58,8 +58,6 @@ FUNCTIONS

     get_permission(role_name, permission_name)

-    get_filter(org_name, name)
-
     get_system_group(org_name, system_group_name)

     get_system(org_name, sys_name, env_name=None, sys_uuid=None)


```

katello-cli-tests-1.3.3-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index 6e17144..7bd14e6 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -35,3 +35,7 @@ CLASSES
         cancel_sync(self, orgName, prodId)

         last_sync_status(self, orgName, prodId)
+
+        update_filters(self, orgName, prodId, filters)
+
+        filters(self, orgName, prodId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index 67151c6..9db8ecf 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -31,5 +31,3 @@ CLASSES
         delete_manifest(self, provId)

         refresh_products(self, provId)
-
-        repo_discovery(self, provId, url)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index b5c4c91..579c917 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -31,6 +31,10 @@ CLASSES

         last_sync_status(self, repo_id)

+        repo_discovery(self, org_name, url, repotype)
+
+        repo_discovery_status(self, discoveryTaskId)
+
         packagegroups(self, repoid)

         packagegroup_by_id(self, repoid, groupId)
@@ -38,3 +42,7 @@ CLASSES
         packagegroupcategories(self, repoid)

         packagegroupcategory_by_id(self, repoid, categoryId)
+
+        update_filters(self, repo_id, filters)
+
+        filters(self, repo_id, inherit=False)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 7a24b35..786e104 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -58,6 +58,8 @@ FUNCTIONS

     get_permission(role_name, permission_name)

+    get_filter(org_name, name)
+
     get_system_group(org_name, system_group_name)

     get_system(org_name, sys_name, env_name=None, sys_uuid=None)


```

katello-cli-tests-1.3.2.pulpv2-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index 7bd14e6..6e17144 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -35,7 +35,3 @@ CLASSES
         cancel_sync(self, orgName, prodId)

         last_sync_status(self, orgName, prodId)
-
-        update_filters(self, orgName, prodId, filters)
-
-        filters(self, orgName, prodId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index 9db8ecf..67151c6 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -31,3 +31,5 @@ CLASSES
         delete_manifest(self, provId)

         refresh_products(self, provId)
+
+        repo_discovery(self, provId, url)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 579c917..b5c4c91 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -31,10 +31,6 @@ CLASSES

         last_sync_status(self, repo_id)

-        repo_discovery(self, org_name, url, repotype)
-
-        repo_discovery_status(self, discoveryTaskId)
-
         packagegroups(self, repoid)

         packagegroup_by_id(self, repoid, groupId)
@@ -42,7 +38,3 @@ CLASSES
         packagegroupcategories(self, repoid)

         packagegroupcategory_by_id(self, repoid, categoryId)
-
-        update_filters(self, repo_id, filters)
-
-        filters(self, repo_id, inherit=False)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 786e104..7a24b35 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -58,8 +58,6 @@ FUNCTIONS

     get_permission(role_name, permission_name)

-    get_filter(org_name, name)
-
     get_system_group(org_name, system_group_name)

     get_system(org_name, sys_name, env_name=None, sys_uuid=None)


```

katello-cli-tests-1.2.1-1
=======================

```diff

diff --git a/katello.client.api.custom_info b/katello.client.api.custom_info
index 5e0cf10..34d786a 100644
--- a/katello.client.api.custom_info
+++ b/katello.client.api.custom_info
@@ -14,7 +14,6 @@ CLASSES
         get_custom_info(self, informable_type, informable_id, keyname=None)

         update_custom_info(self, informable_type, informable_id, keyname,
-                           current_value, value)
+                           new_value)

-        remove_custom_info(self, informable_type, informable_id,
-                           keyname=None, value=None)
+        remove_custom_info(self, informable_type, informable_id, keyname)
diff --git a/katello.client.api.product b/katello.client.api.product
index 12bbbee..7bd14e6 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -14,7 +14,8 @@ CLASSES

         products_by_provider(self, provId, prodName=None)

-        product_by_name(self, orgName, prodName)
+        product_by_name_or_label_or_id(self, orgName, prodName, prodLabel,
+                                       prodId)

         create(self, provId, name, label, description, gpgkey)

diff --git a/katello.client.api.provider b/katello.client.api.provider
index 33c01b8..9db8ecf 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -28,4 +28,6 @@ CLASSES

         import_manifest(self, provId, manifestFile, force=False)

+        delete_manifest(self, provId)
+
         refresh_products(self, provId)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 96b7bc5..786e104 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -38,9 +38,10 @@ FUNCTIONS

     get_library(orgName)

-    get_product(orgName, prodName)
+    get_product(orgName, prodName=None, prodLabel=None, prodId=None)
+        Retrieve product by name, label or id.

-    get_repo(orgName, prodName, repoName, envName=None,
+    get_repo(orgName, prodName, prodLabel, prodId, repoName, envName=None,
              includeDisabled=False)

     get_provider(orgName, provName)


```

katello-cli-tests-1.1.6-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index 305ecc5..533a822 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -11,19 +11,19 @@ CLASSES

         activation_keys_by_environment(self, envId)

-        activation_key(self, keyId)
+        activation_key(self, orgId, keyId)

         create(self, envId, name, description, usage_limit=-1,
                templateId=None)

-        update(self, keyId, environmentId, name, description, templateId,
-               usage_limit)
+        update(self, orgId, keyId, environmentId, name, description,
+               templateId, usage_limit)

-        add_pool(self, keyId, poolid)
+        add_pool(self, orgId, keyId, poolid)

-        remove_pool(self, keyId, poolid)
+        remove_pool(self, orgId, keyId, poolid)

-        delete(self, keyId)
+        delete(self, orgId, keyId)

         add_system_group(self, org_name, activation_key_id, system_group_id)

diff --git a/katello.client.api.custom_info b/katello.client.api.custom_info
index 34d786a..5e0cf10 100644
--- a/katello.client.api.custom_info
+++ b/katello.client.api.custom_info
@@ -14,6 +14,7 @@ CLASSES
         get_custom_info(self, informable_type, informable_id, keyname=None)

         update_custom_info(self, informable_type, informable_id, keyname,
-                           new_value)
+                           current_value, value)

-        remove_custom_info(self, informable_type, informable_id, keyname)
+        remove_custom_info(self, informable_type, informable_id,
+                           keyname=None, value=None)
diff --git a/katello.client.api.environment b/katello.client.api.environment
index 6e3b547..9823c36 100644
--- a/katello.client.api.environment
+++ b/katello.client.api.environment
@@ -16,7 +16,7 @@ CLASSES

         library_by_org(self, orgId)

-        create(self, orgId, name, description, priorId)
+        create(self, orgId, name, label, description, priorId)

         update(self, orgId, envId, name, description, priorId)

diff --git a/katello.client.api.filter b/katello.client.api.filter
index 6dbe013..58ca285 100644
--- a/katello.client.api.filter
+++ b/katello.client.api.filter
@@ -12,6 +12,8 @@ CLASSES

         create(self, org, name, description, filter_list)

+        update(self, org, filter_id, new_name)
+
         delete(self, org, name)

         info(self, org, name)
diff --git a/katello.client.api.organization b/katello.client.api.organization
index c0902b9..6940771 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access Organization Data


-        create(self, name, description)
+        create(self, name, label, description)

         delete(self, name)

diff --git a/katello.client.api.product b/katello.client.api.product
index 12883de..12bbbee 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -16,7 +16,7 @@ CLASSES

         product_by_name(self, orgName, prodName)

-        create(self, provId, name, description, gpgkey)
+        create(self, provId, name, label, description, gpgkey)

         update(self, orgName, prodId, description, gpgkey, nogpgkey,
                gpgkey_recursive)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 5d9d023..579c917 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access repositories


-        create(self, orgName, prod_id, name, url, gpgkey, nogpgkey)
+        create(self, orgName, prod_id, name, label, url, gpgkey, nogpgkey)

         update(self, repo_id, gpgkey, nogpgkey)



```

katello-cli-tests-1.1.4-1
=======================

```diff

diff --git a/katello.client.api.admin b/katello.client.api.admin
index e652a09..368b919 100644
--- a/katello.client.api.admin
+++ b/katello.client.api.admin
@@ -8,4 +8,4 @@ CLASSES
         Various administrative actions


-        crl_regen(self, query={})
+        crl_regen(self, query=None)
diff --git a/katello.client.api.base b/katello.client.api.base
index fad59e8..17707c2 100644
--- a/katello.client.api.base
+++ b/katello.client.api.base
@@ -9,9 +9,9 @@ CLASSES
         after instantiation.


-        update_dict(self, d, key, value)
-            Update value for key in fictionary only if the value is not
-            None.
+        __init__(self)
+            x.__init__(...) initializes x; see x.__class__.__doc__ for
+            signature

         Instance variables

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 5d37049..be92fe9 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,7 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name, type, description=None)
+        create(self, orgName, envId, name, type_in, description=None)

         update(self, csId, newName, description)

diff --git a/katello.client.api.errata b/katello.client.api.errata
index 132bb55..335b161 100644
--- a/katello.client.api.errata
+++ b/katello.client.api.errata
@@ -5,11 +5,12 @@ MODULE NAME
 CLASSES

     class ErrataAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access errata calls


         errata_filter(self, repo_id=None, environment_id=None, prod_id=None,
-                      type=None, severity=None)
+                      type_in=None, severity=None)

-        errata_by_repo(self, repoId, type=None)
+        errata_by_repo(self, repoId, type_in=None)

         errata(self, errata_id, repoId)
diff --git a/katello.client.api.permission b/katello.client.api.permission
index 4fbf368..ae89fcb 100644
--- a/katello.client.api.permission
+++ b/katello.client.api.permission
@@ -8,10 +8,10 @@ CLASSES
         Connection class to access Permissions


-        create(self, roleId, name, description, type, verbs, tagIds,
-               orgId=None)
+        create(self, roleId, name, description, type_in, verbs, tagIds,
+               orgId=None, all_tags=False)

-        permissions(self, roleId, query={})
+        permissions(self, roleId, query=None)

         permission(self, roleId, permissionId)

diff --git a/katello.client.api.sync_plan b/katello.client.api.sync_plan
index 5f5b8e6..fad7b6c 100644
--- a/katello.client.api.sync_plan
+++ b/katello.client.api.sync_plan
@@ -13,7 +13,7 @@ CLASSES
         update(self, org_id, plan_id, name, sync_date, interval,
                description)

-        sync_plans(self, org_id, query={})
+        sync_plans(self, org_id, query=None)

         sync_plan(self, org_id, plan_id)

diff --git a/katello.client.api.system b/katello.client.api.system
index ec9dc2d..688e3cf 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -9,7 +9,7 @@ CLASSES


         register(self, name, org, environment_id, activation_keys, cp_type,
-                 release=None, sla=None, facts={})
+                 release=None, sla=None, facts=None)

         unregister(self, system_uuid)

@@ -17,7 +17,8 @@ CLASSES

         subscriptions(self, system_id)

-        available_pools(self, system_id)
+        available_pools(self, system_id, match_system=False,
+                        match_installed=False, no_overlap=False)

         unsubscribe(self, system_id, entitlement)

@@ -27,7 +28,8 @@ CLASSES

         system(self, system_id)

-        tasks(self, org_name, environment_id, system_name)
+        tasks(self, org_name, environment_id, system_name=None,
+              system_uuid=None)

         packages(self, system_id)

@@ -35,7 +37,7 @@ CLASSES

         releases_for_environment(self, env_id)

-        update(self, system_id, params={})
+        update(self, system_id, params=None)

         install_packages(self, system_id, packages)

@@ -47,15 +49,15 @@ CLASSES

         remove_package_groups(self, system_id, packages)

-        systems_by_org(self, orgId, query={})
+        systems_by_org(self, orgId, query=None)

-        systems_by_env(self, org_name, environment_id, query={})
+        systems_by_env(self, environment_id, query=None)

         errata(self, system_id)

-        report_by_org(self, orgId, format)
+        report_by_org(self, orgId, format_in)

-        report_by_env(self, env_id, format)
+        report_by_env(self, env_id, format_in)

         add_system_groups(self, system_id, system_group_ids)

diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index d105f93..a9dd56d 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -8,15 +8,15 @@ CLASSES
         Connection class to access environment calls


-        system_groups(self, org_id, query={})
+        system_groups(self, org_id, query=None)

-        system_group(self, org_id, system_group_id, query={})
+        system_group(self, org_id, system_group_id, query=None)

         system_group_history(self, org_id, system_group_id, job_id=None)

-        system_group_by_name(self, org_id, system_group_name, query={})
+        system_group_by_name(self, org_id, system_group_name)

-        system_group_systems(self, org_id, system_group_id, query={})
+        system_group_systems(self, org_id, system_group_id, query=None)

         create(self, org_id, name, description, max_systems)

@@ -44,6 +44,6 @@ CLASSES

         remove_package_groups(self, org_id, system_group_id, packages)

-        errata(self, org_id, system_group_id, type=None)
+        errata(self, org_id, system_group_id, type_in=None)

         install_errata(self, org_id, system_group_id, errata)
diff --git a/katello.client.api.template b/katello.client.api.template
index e022517..30d22c0 100644
--- a/katello.client.api.template
+++ b/katello.client.api.template
@@ -15,9 +15,9 @@ CLASSES

         import_tpl(self, envId, description, tplFile)

-        validate_tpl(self, tplId, format)
+        validate_tpl(self, tplId, format_in)

-        export_tpl(self, tplId, format)
+        export_tpl(self, tplId, format_in)

         create(self, envId, name, description, parentId)

diff --git a/katello.client.api.user b/katello.client.api.user
index f7ed01a..1e390b8 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -16,7 +16,7 @@ CLASSES
         update(self, user_id, pw, email, disabled, default_environment,
                default_locale=None)

-        users(self, query={})
+        users(self, query=None)

         user(self, user_id)

@@ -30,4 +30,4 @@ CLASSES

         roles(self, user_id)

-        report(self, format)
+        report(self, format_in)
diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 3751f1f..1eecbca 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -10,7 +10,7 @@ CLASSES

         create(self, name, description)

-        roles(self, query={})
+        roles(self, query=None)

         role(self, role_id)

diff --git a/katello.client.api.utils b/katello.client.api.utils
index b6809a3..96b7bc5 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -61,4 +61,4 @@ FUNCTIONS

     get_system_group(org_name, system_group_name)

-    get_system(org_name, sys_name, env_name=None)
+    get_system(org_name, sys_name, env_name=None, sys_uuid=None)


```

katello-cli-tests-1.1.3-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 96a908a..5d37049 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,7 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name, description=None)
+        create(self, orgName, envId, name, type, description=None)

         update(self, csId, newName, description)

@@ -21,7 +21,7 @@ CLASSES

         dependencies(self, csId)

-        promote(self, csId)
+        apply(self, csId)

         update_content(self, csId, patch)

diff --git a/katello.client.api.user b/katello.client.api.user
index 10a6546..f7ed01a 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,11 +8,13 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, email, disabled, default_environment)
+        create(self, name, pw, email, disabled, default_environment,
+               default_locale=None)

         delete(self, user_id)

-        update(self, user_id, pw, email, disabled, default_environment)
+        update(self, user_id, pw, email, disabled, default_environment,
+               default_locale=None)

         users(self, query={})



```

katello-cli-tests-1.1.2-1
=======================

```diff

diff --git a/katello.client.api.utils b/katello.client.api.utils
index f8cc14a..b6809a3 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -1,12 +1,20 @@
 MODULE NAME
     katello.client.api.utils

+DESCRIPTION
+    Bunch of api utilities for finding records by their names. Katello API
+    uses integer ids for record identification in most cases. These util
+    functions help with translating names to ids. All of them throw
+    ApiDataError if any of the records is not found.
+

 CLASSES

     class ApiDataError(exceptions.Exception)
         Exception to indicate an error in search for data via api. The only
-        argument is [0] the error message.
+        argument is the error message.
+
+        :argument: localized error message

         Inherited methods
             exceptions.BaseException.__delattr__(...)


```

katello-cli-tests-0.2.21-1
=======================

```diff

diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index d1103e5..d105f93 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -18,12 +18,11 @@ CLASSES

         system_group_systems(self, org_id, system_group_id, query={})

-        lock(self, org_id, system_group_id, query={})
-
-        unlock(self, org_id, system_group_id, query={})
-
         create(self, org_id, name, description, max_systems)

+        copy(self, org_id, system_group_id, new_name, description,
+             max_systems)
+
         update(self, org_id, system_group_id, name, description,
                max_systems)

@@ -45,4 +44,6 @@ CLASSES

         remove_package_groups(self, org_id, system_group_id, packages)

+        errata(self, org_id, system_group_id, type=None)
+
         install_errata(self, org_id, system_group_id, errata)


```

katello-cli-tests-0.2.20-1
=======================

```diff

diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index 7e1aaf1..d1103e5 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -12,7 +12,7 @@ CLASSES

         system_group(self, org_id, system_group_id, query={})

-        system_group_history(self, org_id, system_group_id, query={})
+        system_group_history(self, org_id, system_group_id, job_id=None)

         system_group_by_name(self, org_id, system_group_name, query={})

@@ -32,3 +32,17 @@ CLASSES
         add_systems(self, org_id, system_group_id, system_ids)

         remove_systems(self, org_id, system_group_id, system_ids)
+
+        install_packages(self, org_id, system_group_id, packages)
+
+        update_packages(self, org_id, system_group_id, packages)
+
+        remove_packages(self, org_id, system_group_id, packages)
+
+        install_package_groups(self, org_id, system_group_id, packages)
+
+        update_package_groups(self, org_id, system_group_id, packages)
+
+        remove_package_groups(self, org_id, system_group_id, packages)
+
+        install_errata(self, org_id, system_group_id, errata)


```

katello-cli-tests-0.2.18-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index 87092fb..305ecc5 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -13,9 +13,11 @@ CLASSES

         activation_key(self, keyId)

-        create(self, envId, name, description, templateId=None)
+        create(self, envId, name, description, usage_limit=-1,
+               templateId=None)

-        update(self, keyId, environmentId, name, description, templateId)
+        update(self, keyId, environmentId, name, description, templateId,
+               usage_limit)

         add_pool(self, keyId, poolid)

diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index ef5d0bf..7e1aaf1 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -27,7 +27,7 @@ CLASSES
         update(self, org_id, system_group_id, name, description,
                max_systems)

-        delete(self, org_id, system_group_id)
+        delete(self, org_id, system_group_id, delete_systems)

         add_systems(self, org_id, system_group_id, system_ids)



```

katello-cli-tests-0.2.17-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index b5b5f7d..87092fb 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -22,3 +22,8 @@ CLASSES
         remove_pool(self, keyId, poolid)

         delete(self, keyId)
+
+        add_system_group(self, org_name, activation_key_id, system_group_id)
+
+        remove_system_group(self, org_name, activation_key_id,
+                            system_group_id)
diff --git a/katello.client.api.system b/katello.client.api.system
index 13d705f..ec9dc2d 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -57,4 +57,8 @@ CLASSES

         report_by_env(self, env_id, format)

+        add_system_groups(self, system_id, system_group_ids)
+
+        remove_system_groups(self, system_id, system_group_ids)
+
         remove_consumer_deletion_record(self, uuid)
diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index d105f93..ef5d0bf 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -12,38 +12,23 @@ CLASSES

         system_group(self, org_id, system_group_id, query={})

-        system_group_history(self, org_id, system_group_id, job_id=None)
+        system_group_history(self, org_id, system_group_id, query={})

         system_group_by_name(self, org_id, system_group_name, query={})

         system_group_systems(self, org_id, system_group_id, query={})

-        create(self, org_id, name, description, max_systems)
+        lock(self, org_id, system_group_id, query={})
+
+        unlock(self, org_id, system_group_id, query={})

-        copy(self, org_id, system_group_id, new_name, description,
-             max_systems)
+        create(self, org_id, name, description, max_systems)

         update(self, org_id, system_group_id, name, description,
                max_systems)

-        delete(self, org_id, system_group_id, delete_systems)
+        delete(self, org_id, system_group_id)

         add_systems(self, org_id, system_group_id, system_ids)

         remove_systems(self, org_id, system_group_id, system_ids)
-
-        install_packages(self, org_id, system_group_id, packages)
-
-        update_packages(self, org_id, system_group_id, packages)
-
-        remove_packages(self, org_id, system_group_id, packages)
-
-        install_package_groups(self, org_id, system_group_id, packages)
-
-        update_package_groups(self, org_id, system_group_id, packages)
-
-        remove_package_groups(self, org_id, system_group_id, packages)
-
-        errata(self, org_id, system_group_id, type=None)
-
-        install_errata(self, org_id, system_group_id, errata)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 7a0fb62..f8cc14a 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -51,4 +51,6 @@ FUNCTIONS

     get_filter(org_name, name)

+    get_system_group(org_name, system_group_name)
+
     get_system(org_name, sys_name, env_name=None)


```

katello-cli-tests-0.2.16-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index 07a6679..13d705f 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -56,3 +56,5 @@ CLASSES
         report_by_org(self, orgId, format)

         report_by_env(self, env_id, format)
+
+        remove_consumer_deletion_record(self, uuid)


```

katello-cli-tests-0.2.15-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index b1a5fbb..07a6679 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access environment calls


-        register(self, name, org, envName, activation_keys, cp_type,
+        register(self, name, org, environment_id, activation_keys, cp_type,
                  release=None, sla=None, facts={})

         unregister(self, system_uuid)
@@ -27,7 +27,7 @@ CLASSES

         system(self, system_id)

-        tasks(self, org_name, env_name, system_name)
+        tasks(self, org_name, environment_id, system_name)

         packages(self, system_id)

@@ -49,7 +49,7 @@ CLASSES

         systems_by_org(self, orgId, query={})

-        systems_by_env(self, orgId, envName, query={})
+        systems_by_env(self, org_name, environment_id, query={})

         errata(self, system_id)

diff --git a/katello.client.api.utils b/katello.client.api.utils
index 1824ece..7a0fb62 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -2,6 +2,26 @@ MODULE NAME
     katello.client.api.utils


+CLASSES
+
+    class ApiDataError(exceptions.Exception)
+        Exception to indicate an error in search for data via api. The only
+        argument is [0] the error message.
+
+        Inherited methods
+            exceptions.BaseException.__delattr__(...)
+            exceptions.BaseException.__getattribute__(...)
+            exceptions.BaseException.__getitem__(x, y)
+            exceptions.BaseException.__getslice__(x, i, j)
+            exceptions.Exception.__init__(...)
+            exceptions.Exception.__new__(T, S, *...)
+            exceptions.BaseException.__reduce__(...)
+            exceptions.BaseException.__repr__(x)
+            exceptions.BaseException.__setattr__(...)
+            exceptions.BaseException.__setstate__(...)
+            exceptions.BaseException.__str__(x)
+            exceptions.BaseException.__unicode__(...)
+
 FUNCTIONS

     get_organization(orgName)
@@ -30,3 +50,5 @@ FUNCTIONS
     get_permission(role_name, permission_name)

     get_filter(org_name, name)
+
+    get_system(org_name, sys_name, env_name=None)


```

katello-cli-tests-0.2.14-1
=======================

```diff

diff --git a/katello.client.api.package b/katello.client.api.package
index f41475f..a45d238 100644
--- a/katello.client.api.package
+++ b/katello.client.api.package
@@ -11,3 +11,5 @@ CLASSES
         package(self, packageId, repoId)

         packages_by_repo(self, repoId)
+
+        search(self, query, repoId)
diff --git a/katello.client.api.user b/katello.client.api.user
index aa7ea4d..10a6546 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -20,6 +20,8 @@ CLASSES

         user_by_name(self, user_name)

+        sync_ldap_roles(self)
+
         assign_role(self, user_id, role_id)

         unassign_role(self, user_id, role_id)
diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 3476461..3751f1f 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -21,3 +21,9 @@ CLASSES
         update(self, role_id, name, desc)

         available_verbs(self, orgName)
+
+        add_ldap_group(self, role_id, group_name)
+
+        remove_ldap_group(self, role_id, group_name)
+
+        ldap_groups(self, role_id)


```

katello-cli-tests-0.2.13-1
=======================

```diff

diff --git a/katello.client.api.user b/katello.client.api.user
index 882dbe3..aa7ea4d 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,11 +8,11 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, email, disabled)
+        create(self, name, pw, email, disabled, default_environment)

         delete(self, user_id)

-        update(self, user_id, pw, email, disabled)
+        update(self, user_id, pw, email, disabled, default_environment)

         users(self, query={})



```

katello-cli-tests-0.2.12-1
=======================

```diff

diff --git a/katello.client.api.organization b/katello.client.api.organization
index da1a42c..c0902b9 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -18,6 +18,6 @@ CLASSES

         organization(self, name)

-        uebercert(self, name)
+        uebercert(self, name, regenerate=False)

         pools(self, name)
diff --git a/katello.client.api.system b/katello.client.api.system
index b8f3d13..b1a5fbb 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -9,7 +9,7 @@ CLASSES


         register(self, name, org, envName, activation_keys, cp_type,
-                 release=None, sla=None)
+                 release=None, sla=None, facts={})

         unregister(self, system_uuid)



```

katello-cli-tests-0.2.9-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index 12dea52..b8f3d13 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -9,7 +9,7 @@ CLASSES


         register(self, name, org, envName, activation_keys, cp_type,
-                 release=None)
+                 release=None, sla=None)

         unregister(self, system_uuid)



```

katello-cli-tests-0.2.7-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index 89a515f..12dea52 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -8,7 +8,8 @@ CLASSES
         Connection class to access environment calls


-        register(self, name, org, envName, activation_keys, cp_type)
+        register(self, name, org, envName, activation_keys, cp_type,
+                 release=None)

         unregister(self, system_uuid)

@@ -30,6 +31,10 @@ CLASSES

         packages(self, system_id)

+        releases_for_system(self, system_id)
+
+        releases_for_environment(self, env_id)
+
         update(self, system_id, params={})

         install_packages(self, system_id, packages)


```

katello-cli-tests-0.2.6-1
=======================

```diff

diff --git a/katello.client.api.provider b/katello.client.api.provider
index ff2a1d0..33c01b8 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -27,3 +27,5 @@ CLASSES
         last_sync_status(self, provId)

         import_manifest(self, provId, manifestFile, force=False)
+
+        refresh_products(self, provId)


```

katello-cli-tests-0.2.5-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 1853fc2..96a908a 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -15,6 +15,8 @@ CLASSES

         create(self, orgName, envId, name, description=None)

+        update(self, csId, newName, description)
+
         delete(self, csId)

         dependencies(self, csId)


```

katello-cli-tests-0.2.4-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index d846476..89a515f 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -50,4 +50,4 @@ CLASSES

         report_by_org(self, orgId, format)

-        report_by_env(self, orgId, envName, format)
+        report_by_env(self, env_id, format)


```

katello-cli-tests-0.2.3-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 352c253..1853fc2 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,7 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name)
+        create(self, orgName, envId, name, description=None)

         delete(self, csId)



```

katello-cli-tests-0.2.1-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 96a908a..352c253 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,9 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name, description=None)
-
-        update(self, csId, newName, description)
+        create(self, orgName, envId, name)

         delete(self, csId)

diff --git a/katello.client.api.system b/katello.client.api.system
index 89a515f..d846476 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -50,4 +50,4 @@ CLASSES

         report_by_org(self, orgId, format)

-        report_by_env(self, env_id, format)
+        report_by_env(self, orgId, envName, format)


```

katello-cli-tests-0.1.32-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 1853fc2..96a908a 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -15,6 +15,8 @@ CLASSES

         create(self, orgName, envId, name, description=None)

+        update(self, csId, newName, description)
+
         delete(self, csId)

         dependencies(self, csId)


```

katello-cli-tests-0.1.31-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 352c253..1853fc2 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,7 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name)
+        create(self, orgName, envId, name, description=None)

         delete(self, csId)

diff --git a/katello.client.api.system b/katello.client.api.system
index d846476..89a515f 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -50,4 +50,4 @@ CLASSES

         report_by_org(self, orgId, format)

-        report_by_env(self, orgId, envName, format)
+        report_by_env(self, env_id, format)


```

katello-cli-tests-0.1.25-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index a9f81be..12883de 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -18,23 +18,23 @@ CLASSES

         create(self, provId, name, description, gpgkey)

-        update(self, prodId, description, gpgkey, nogpgkey,
+        update(self, orgName, prodId, description, gpgkey, nogpgkey,
                gpgkey_recursive)

-        show(self, prodId)
+        show(self, orgName, prodId)

-        delete(self, prodId)
+        delete(self, orgName, prodId)

-        sync(self, prodId)
+        sync(self, orgName, prodId)

-        set_sync_plan(self, prodId, planId)
+        set_sync_plan(self, orgName, prodId, planId)

-        remove_sync_plan(self, prodId)
+        remove_sync_plan(self, orgName, prodId)

-        cancel_sync(self, prodId)
+        cancel_sync(self, orgName, prodId)

-        last_sync_status(self, prodId)
+        last_sync_status(self, orgName, prodId)

-        update_filters(self, prodId, filters)
+        update_filters(self, orgName, prodId, filters)

-        filters(self, prodId)
+        filters(self, orgName, prodId)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index c9a4885..5d9d023 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access repositories


-        create(self, prod_id, name, url, gpgkey, nogpgkey)
+        create(self, orgName, prod_id, name, url, gpgkey, nogpgkey)

         update(self, repo_id, gpgkey, nogpgkey)

@@ -17,7 +17,7 @@ CLASSES
         repos_by_env_product(self, envId, productId, name=None,
                              includeDisabled=False)

-        repos_by_product(self, productId, includeDisabled=False)
+        repos_by_product(self, orgName, productId, includeDisabled=False)

         repo(self, repo_id)



```

katello-cli-tests-0.1.23-1
=======================

```diff

diff --git a/katello.client.api.repo b/katello.client.api.repo
index 9229493..c9a4885 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -45,4 +45,4 @@ CLASSES

         update_filters(self, repo_id, filters)

-        filters(self, repo_id)
+        filters(self, repo_id, inherit=False)


```

katello-cli-tests-0.1.22-1
=======================

```diff

diff --git a/katello.client.api.environment b/katello.client.api.environment
index cc99ace..6e3b547 100644
--- a/katello.client.api.environment
+++ b/katello.client.api.environment
@@ -14,7 +14,7 @@ CLASSES

         environment_by_name(self, orgId, envName)

-        locker_by_org(self, orgId)
+        library_by_org(self, orgId)

         create(self, orgId, name, description, priorId)

diff --git a/katello.client.api.repo b/katello.client.api.repo
index dba79c6..9229493 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -42,3 +42,7 @@ CLASSES
         packagegroupcategories(self, repoid)

         packagegroupcategory_by_id(self, repoid, categoryId)
+
+        update_filters(self, repo_id, filters)
+
+        filters(self, repo_id)
diff --git a/katello.client.api.system b/katello.client.api.system
index ef4de05..d846476 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -18,7 +18,11 @@ CLASSES

         available_pools(self, system_id)

-        unsubscribe(self, system_id, pool)
+        unsubscribe(self, system_id, entitlement)
+
+        unsubscribe_by_serial(self, system_id, serial)
+
+        unsubscribe_all(self, system_id)

         system(self, system_id)

diff --git a/katello.client.api.utils b/katello.client.api.utils
index e1c0dd7..1824ece 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -8,7 +8,7 @@ FUNCTIONS

     get_environment(orgName, envName=None)

-    get_locker(orgName)
+    get_library(orgName)

     get_product(orgName, prodName)

@@ -28,3 +28,5 @@ FUNCTIONS
     get_sync_plan(org_name, name)

     get_permission(role_name, permission_name)
+
+    get_filter(org_name, name)


```

katello-cli-tests-0.1.20-1
=======================

```diff

diff --git a/katello.client.api.errata b/katello.client.api.errata
index 6798d51..132bb55 100644
--- a/katello.client.api.errata
+++ b/katello.client.api.errata
@@ -12,4 +12,4 @@ CLASSES

         errata_by_repo(self, repoId, type=None)

-        errata(self, errata_id)
+        errata(self, errata_id, repoId)
diff --git a/katello.client.api.package b/katello.client.api.package
index 99e9978..f41475f 100644
--- a/katello.client.api.package
+++ b/katello.client.api.package
@@ -8,6 +8,6 @@ CLASSES
         Connection class to access package calls


-        package(self, packageId)
+        package(self, packageId, repoId)

         packages_by_repo(self, repoId)


```

katello-cli-tests-0.1.19-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index 81a73e1..a9f81be 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -16,7 +16,10 @@ CLASSES

         product_by_name(self, orgName, prodName)

-        create(self, provId, name, description)
+        create(self, provId, name, description, gpgkey)
+
+        update(self, prodId, description, gpgkey, nogpgkey,
+               gpgkey_recursive)

         show(self, prodId)

diff --git a/katello.client.api.repo b/katello.client.api.repo
index 116a3a8..dba79c6 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,7 +8,9 @@ CLASSES
         Connection class to access repositories


-        create(self, prod_id, name, url)
+        create(self, prod_id, name, url, gpgkey, nogpgkey)
+
+        update(self, repo_id, gpgkey, nogpgkey)

         repos_by_org_env(self, orgName, envId, includeDisabled=False)



```

katello-cli-tests-0.1.18-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index c668ac9..81a73e1 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -24,6 +24,10 @@ CLASSES

         sync(self, prodId)

+        set_sync_plan(self, prodId, planId)
+
+        remove_sync_plan(self, prodId)
+
         cancel_sync(self, prodId)

         last_sync_status(self, prodId)


```

katello-cli-tests-0.1.17-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 772b3f2..352c253 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -17,6 +17,8 @@ CLASSES

         delete(self, csId)

+        dependencies(self, csId)
+
         promote(self, csId)

         update_content(self, csId, patch)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index f489c38..e1c0dd7 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -25,4 +25,6 @@ FUNCTIONS

     get_role(name)

+    get_sync_plan(org_name, name)
+
     get_permission(role_name, permission_name)


```

katello-cli-tests-0.1.16-1
=======================

```diff

diff --git a/katello.client.api.distribution b/katello.client.api.distribution
index b83fde6..d39910d 100644
--- a/katello.client.api.distribution
+++ b/katello.client.api.distribution
@@ -10,4 +10,4 @@ CLASSES

         distributions_by_repo(self, repoId)

-        distribution(self, distribution_id)
+        distribution(self, repoId, distribution_id)
diff --git a/katello.client.api.system b/katello.client.api.system
index 47100d8..ef4de05 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -22,10 +22,22 @@ CLASSES

         system(self, system_id)

+        tasks(self, org_name, env_name, system_name)
+
         packages(self, system_id)

         update(self, system_id, params={})

+        install_packages(self, system_id, packages)
+
+        remove_packages(self, system_id, packages)
+
+        update_packages(self, system_id, packages)
+
+        install_package_groups(self, system_id, packages)
+
+        remove_package_groups(self, system_id, packages)
+
         systems_by_org(self, orgId, query={})

         systems_by_env(self, orgId, envName, query={})
diff --git a/katello.client.api.task_status b/katello.client.api.task_status
index 65c5bab..89273c1 100644
--- a/katello.client.api.task_status
+++ b/katello.client.api.task_status
@@ -8,3 +8,8 @@ CLASSES


         status(self, taskUuid)
+
+    class SystemTaskStatusAPI(katello.client.api.base.KatelloAPI)
+
+
+        status(self, taskUuid)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 271859d..f489c38 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -22,3 +22,7 @@ FUNCTIONS
     get_changeset(orgName, envName, csName)

     get_user(userName)
+
+    get_role(name)
+
+    get_permission(role_name, permission_name)


```

katello-cli-tests-0.1.15-1
=======================

```diff

diff --git a/katello.client.api.user b/katello.client.api.user
index 0f6d753..882dbe3 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -18,4 +18,12 @@ CLASSES

         user(self, user_id)

+        user_by_name(self, user_name)
+
+        assign_role(self, user_id, role_id)
+
+        unassign_role(self, user_id, role_id)
+
+        roles(self, user_id)
+
         report(self, format)
diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 4e0d0c7..3476461 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -19,3 +19,5 @@ CLASSES
         delete(self, role_id)

         update(self, role_id, name, desc)
+
+        available_verbs(self, orgName)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index e85014a..271859d 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -20,3 +20,5 @@ FUNCTIONS
     get_template(orgName, envName, tplName)

     get_changeset(orgName, envName, csName)
+
+    get_user(userName)


```

katello-cli-tests-0.1.14-1
=======================

```diff

diff --git a/katello.client.api.utils b/katello.client.api.utils
index f405cb8..e85014a 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -12,7 +12,8 @@ FUNCTIONS

     get_product(orgName, prodName)

-    get_repo(orgName, prodName, repoName, envName=None)
+    get_repo(orgName, prodName, repoName, envName=None,
+             includeDisabled=False)

     get_provider(orgName, provName)



```

katello-cli-tests-0.1.13-1
=======================

```diff

diff --git a/katello.client.api.provider b/katello.client.api.provider
index aa1e4c1..ff2a1d0 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -26,4 +26,4 @@ CLASSES

         last_sync_status(self, provId)

-        import_manifest(self, provId, manifestFile)
+        import_manifest(self, provId, manifestFile, force=False)


```

katello-cli-tests-0.1.12-1
=======================

```diff

diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 3476461..4e0d0c7 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -19,5 +19,3 @@ CLASSES
         delete(self, role_id)

         update(self, role_id, name, desc)
-
-        available_verbs(self, orgName)


```

katello-cli-tests-0.1.10-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index b1eb88f..772b3f2 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -20,3 +20,7 @@ CLASSES
         promote(self, csId)

         update_content(self, csId, patch)
+
+        add_content(self, csId, contentType, attrs)
+
+        remove_content(self, csId, contentType, attrs)


```

katello-cli-tests-0.1.7-1
=======================

```diff

diff --git a/katello.client.api.organization b/katello.client.api.organization
index ca37547..da1a42c 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -18,8 +18,6 @@ CLASSES

         organization(self, name)

-        generate_uebercert(self, name)
-
         uebercert(self, name)

         pools(self, name)
diff --git a/katello.client.api.product b/katello.client.api.product
index f8a71c9..c668ac9 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -24,6 +24,8 @@ CLASSES

         sync(self, prodId)

+        cancel_sync(self, prodId)
+
         last_sync_status(self, prodId)

         update_filters(self, prodId, filters)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index beabb27..aa1e4c1 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -5,7 +5,7 @@ MODULE NAME
 CLASSES

     class ProviderAPI(katello.client.api.base.KatelloAPI)
-        Connection class to access repo specific calls
+        Connection class to access provider specific calls


         create(self, name, orgName, description=None, pType=None, url=None)
@@ -22,6 +22,8 @@ CLASSES

         sync(self, provId)

+        cancel_sync(self, provId)
+
         last_sync_status(self, provId)

         import_manifest(self, provId, manifestFile)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index b904e7b..116a3a8 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -10,18 +10,23 @@ CLASSES

         create(self, prod_id, name, url)

-        repos_by_org_env(self, orgName, envId)
+        repos_by_org_env(self, orgName, envId, includeDisabled=False)

-        repos_by_env_product(self, envId, productId, name=None)
+        repos_by_env_product(self, envId, productId, name=None,
+                             includeDisabled=False)

-        repos_by_product(self, productId)
+        repos_by_product(self, productId, includeDisabled=False)

         repo(self, repo_id)

+        enable(self, repo_id, enable=True)
+
         delete(self, repoId)

         sync(self, repo_id)

+        cancel_sync(self, repo_id)
+
         last_sync_status(self, repo_id)

         repo_discovery(self, org_name, url, repotype)
diff --git a/katello.client.api.template b/katello.client.api.template
index 0355c7c..e022517 100644
--- a/katello.client.api.template
+++ b/katello.client.api.template
@@ -15,6 +15,8 @@ CLASSES

         import_tpl(self, envId, description, tplFile)

+        validate_tpl(self, tplId, format)
+
         export_tpl(self, tplId, format)

         create(self, envId, name, description, parentId)
@@ -28,3 +30,7 @@ CLASSES
         promotion_status(self, task_id)

         delete(self, template_id)
+
+        Class variables
+
+            format_content_type = {'json': 'application/json', 'tdl': 'a...


```

katello-cli-tests-0.1.4-1
=======================

```diff

diff --git a/katello.client.api.user b/katello.client.api.user
index 689419b..0f6d753 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,11 +8,11 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, disabled)
+        create(self, name, pw, email, disabled)

         delete(self, user_id)

-        update(self, user_id, pw, disabled)
+        update(self, user_id, pw, email, disabled)

         users(self, query={})



```

katello-cli-tests-0.1.2-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 1853fc2..b1eb88f 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,16 +13,10 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name, description=None)
+        create(self, orgName, envId, name)

         delete(self, csId)

-        dependencies(self, csId)
-
         promote(self, csId)

         update_content(self, csId, patch)
-
-        add_content(self, csId, contentType, attrs)
-
-        remove_content(self, csId, contentType, attrs)
diff --git a/katello.client.api.distribution b/katello.client.api.distribution
index d39910d..b83fde6 100644
--- a/katello.client.api.distribution
+++ b/katello.client.api.distribution
@@ -10,4 +10,4 @@ CLASSES

         distributions_by_repo(self, repoId)

-        distribution(self, repoId, distribution_id)
+        distribution(self, distribution_id)
diff --git a/katello.client.api.environment b/katello.client.api.environment
index 6e3b547..cc99ace 100644
--- a/katello.client.api.environment
+++ b/katello.client.api.environment
@@ -14,7 +14,7 @@ CLASSES

         environment_by_name(self, orgId, envName)

-        library_by_org(self, orgId)
+        locker_by_org(self, orgId)

         create(self, orgId, name, description, priorId)

diff --git a/katello.client.api.errata b/katello.client.api.errata
index 132bb55..6798d51 100644
--- a/katello.client.api.errata
+++ b/katello.client.api.errata
@@ -12,4 +12,4 @@ CLASSES

         errata_by_repo(self, repoId, type=None)

-        errata(self, errata_id, repoId)
+        errata(self, errata_id)
diff --git a/katello.client.api.organization b/katello.client.api.organization
index da1a42c..ca37547 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -18,6 +18,8 @@ CLASSES

         organization(self, name)

+        generate_uebercert(self, name)
+
         uebercert(self, name)

         pools(self, name)
diff --git a/katello.client.api.package b/katello.client.api.package
index f41475f..99e9978 100644
--- a/katello.client.api.package
+++ b/katello.client.api.package
@@ -8,6 +8,6 @@ CLASSES
         Connection class to access package calls


-        package(self, packageId, repoId)
+        package(self, packageId)

         packages_by_repo(self, repoId)
diff --git a/katello.client.api.product b/katello.client.api.product
index 12883de..f8a71c9 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -16,25 +16,16 @@ CLASSES

         product_by_name(self, orgName, prodName)

-        create(self, provId, name, description, gpgkey)
+        create(self, provId, name, description)

-        update(self, orgName, prodId, description, gpgkey, nogpgkey,
-               gpgkey_recursive)
+        show(self, prodId)

-        show(self, orgName, prodId)
+        delete(self, prodId)

-        delete(self, orgName, prodId)
+        sync(self, prodId)

-        sync(self, orgName, prodId)
+        last_sync_status(self, prodId)

-        set_sync_plan(self, orgName, prodId, planId)
+        update_filters(self, prodId, filters)

-        remove_sync_plan(self, orgName, prodId)
-
-        cancel_sync(self, orgName, prodId)
-
-        last_sync_status(self, orgName, prodId)
-
-        update_filters(self, orgName, prodId, filters)
-
-        filters(self, orgName, prodId)
+        filters(self, prodId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index ff2a1d0..beabb27 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -5,7 +5,7 @@ MODULE NAME
 CLASSES

     class ProviderAPI(katello.client.api.base.KatelloAPI)
-        Connection class to access provider specific calls
+        Connection class to access repo specific calls


         create(self, name, orgName, description=None, pType=None, url=None)
@@ -22,8 +22,6 @@ CLASSES

         sync(self, provId)

-        cancel_sync(self, provId)
-
         last_sync_status(self, provId)

-        import_manifest(self, provId, manifestFile, force=False)
+        import_manifest(self, provId, manifestFile)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 5d9d023..b904e7b 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,27 +8,20 @@ CLASSES
         Connection class to access repositories


-        create(self, orgName, prod_id, name, url, gpgkey, nogpgkey)
+        create(self, prod_id, name, url)

-        update(self, repo_id, gpgkey, nogpgkey)
+        repos_by_org_env(self, orgName, envId)

-        repos_by_org_env(self, orgName, envId, includeDisabled=False)
+        repos_by_env_product(self, envId, productId, name=None)

-        repos_by_env_product(self, envId, productId, name=None,
-                             includeDisabled=False)
-
-        repos_by_product(self, orgName, productId, includeDisabled=False)
+        repos_by_product(self, productId)

         repo(self, repo_id)

-        enable(self, repo_id, enable=True)
-
         delete(self, repoId)

         sync(self, repo_id)

-        cancel_sync(self, repo_id)
-
         last_sync_status(self, repo_id)

         repo_discovery(self, org_name, url, repotype)
@@ -42,7 +35,3 @@ CLASSES
         packagegroupcategories(self, repoid)

         packagegroupcategory_by_id(self, repoid, categoryId)
-
-        update_filters(self, repo_id, filters)
-
-        filters(self, repo_id, inherit=False)
diff --git a/katello.client.api.system b/katello.client.api.system
index 89a515f..47100d8 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -18,30 +18,14 @@ CLASSES

         available_pools(self, system_id)

-        unsubscribe(self, system_id, entitlement)
-
-        unsubscribe_by_serial(self, system_id, serial)
-
-        unsubscribe_all(self, system_id)
+        unsubscribe(self, system_id, pool)

         system(self, system_id)

-        tasks(self, org_name, env_name, system_name)
-
         packages(self, system_id)

         update(self, system_id, params={})

-        install_packages(self, system_id, packages)
-
-        remove_packages(self, system_id, packages)
-
-        update_packages(self, system_id, packages)
-
-        install_package_groups(self, system_id, packages)
-
-        remove_package_groups(self, system_id, packages)
-
         systems_by_org(self, orgId, query={})

         systems_by_env(self, orgId, envName, query={})
@@ -50,4 +34,4 @@ CLASSES

         report_by_org(self, orgId, format)

-        report_by_env(self, env_id, format)
+        report_by_env(self, orgId, envName, format)
diff --git a/katello.client.api.task_status b/katello.client.api.task_status
index 89273c1..65c5bab 100644
--- a/katello.client.api.task_status
+++ b/katello.client.api.task_status
@@ -8,8 +8,3 @@ CLASSES


         status(self, taskUuid)
-
-    class SystemTaskStatusAPI(katello.client.api.base.KatelloAPI)
-
-
-        status(self, taskUuid)
diff --git a/katello.client.api.template b/katello.client.api.template
index e022517..0355c7c 100644
--- a/katello.client.api.template
+++ b/katello.client.api.template
@@ -15,8 +15,6 @@ CLASSES

         import_tpl(self, envId, description, tplFile)

-        validate_tpl(self, tplId, format)
-
         export_tpl(self, tplId, format)

         create(self, envId, name, description, parentId)
@@ -30,7 +28,3 @@ CLASSES
         promotion_status(self, task_id)

         delete(self, template_id)
-
-        Class variables
-
-            format_content_type = {'json': 'application/json', 'tdl': 'a...
diff --git a/katello.client.api.user b/katello.client.api.user
index 882dbe3..689419b 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,22 +8,14 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, email, disabled)
+        create(self, name, pw, disabled)

         delete(self, user_id)

-        update(self, user_id, pw, email, disabled)
+        update(self, user_id, pw, disabled)

         users(self, query={})

         user(self, user_id)

-        user_by_name(self, user_name)
-
-        assign_role(self, user_id, role_id)
-
-        unassign_role(self, user_id, role_id)
-
-        roles(self, user_id)
-
         report(self, format)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 1824ece..f405cb8 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -8,25 +8,14 @@ FUNCTIONS

     get_environment(orgName, envName=None)

-    get_library(orgName)
+    get_locker(orgName)

     get_product(orgName, prodName)

-    get_repo(orgName, prodName, repoName, envName=None,
-             includeDisabled=False)
+    get_repo(orgName, prodName, repoName, envName=None)

     get_provider(orgName, provName)

     get_template(orgName, envName, tplName)

     get_changeset(orgName, envName, csName)
-
-    get_user(userName)
-
-    get_role(name)
-
-    get_sync_plan(org_name, name)
-
-    get_permission(role_name, permission_name)
-
-    get_filter(org_name, name)


```

katello-cli-tests
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index 305ecc5..b5b5f7d 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -13,19 +13,12 @@ CLASSES

         activation_key(self, keyId)

-        create(self, envId, name, description, usage_limit=-1,
-               templateId=None)
+        create(self, envId, name, description, templateId=None)

-        update(self, keyId, environmentId, name, description, templateId,
-               usage_limit)
+        update(self, keyId, environmentId, name, description, templateId)

         add_pool(self, keyId, poolid)

         remove_pool(self, keyId, poolid)

         delete(self, keyId)
-
-        add_system_group(self, org_name, activation_key_id, system_group_id)
-
-        remove_system_group(self, org_name, activation_key_id,
-                            system_group_id)
diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 5d37049..1853fc2 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,15 +13,13 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name, type, description=None)
-
-        update(self, csId, newName, description)
+        create(self, orgName, envId, name, description=None)

         delete(self, csId)

         dependencies(self, csId)

-        apply(self, csId)
+        promote(self, csId)

         update_content(self, csId, patch)

diff --git a/katello.client.api.organization b/katello.client.api.organization
index c0902b9..da1a42c 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -18,6 +18,6 @@ CLASSES

         organization(self, name)

-        uebercert(self, name, regenerate=False)
+        uebercert(self, name)

         pools(self, name)
diff --git a/katello.client.api.package b/katello.client.api.package
index a45d238..f41475f 100644
--- a/katello.client.api.package
+++ b/katello.client.api.package
@@ -11,5 +11,3 @@ CLASSES
         package(self, packageId, repoId)

         packages_by_repo(self, repoId)
-
-        search(self, query, repoId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index 33c01b8..ff2a1d0 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -27,5 +27,3 @@ CLASSES
         last_sync_status(self, provId)

         import_manifest(self, provId, manifestFile, force=False)
-
-        refresh_products(self, provId)
diff --git a/katello.client.api.system b/katello.client.api.system
index ec9dc2d..89a515f 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -8,8 +8,7 @@ CLASSES
         Connection class to access environment calls


-        register(self, name, org, environment_id, activation_keys, cp_type,
-                 release=None, sla=None, facts={})
+        register(self, name, org, envName, activation_keys, cp_type)

         unregister(self, system_uuid)

@@ -27,14 +26,10 @@ CLASSES

         system(self, system_id)

-        tasks(self, org_name, environment_id, system_name)
+        tasks(self, org_name, env_name, system_name)

         packages(self, system_id)

-        releases_for_system(self, system_id)
-
-        releases_for_environment(self, env_id)
-
         update(self, system_id, params={})

         install_packages(self, system_id, packages)
@@ -49,16 +44,10 @@ CLASSES

         systems_by_org(self, orgId, query={})

-        systems_by_env(self, org_name, environment_id, query={})
+        systems_by_env(self, orgId, envName, query={})

         errata(self, system_id)

         report_by_org(self, orgId, format)

         report_by_env(self, env_id, format)
-
-        add_system_groups(self, system_id, system_group_ids)
-
-        remove_system_groups(self, system_id, system_group_ids)
-
-        remove_consumer_deletion_record(self, uuid)
diff --git a/katello.client.api.user b/katello.client.api.user
index f7ed01a..882dbe3 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,13 +8,11 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, email, disabled, default_environment,
-               default_locale=None)
+        create(self, name, pw, email, disabled)

         delete(self, user_id)

-        update(self, user_id, pw, email, disabled, default_environment,
-               default_locale=None)
+        update(self, user_id, pw, email, disabled)

         users(self, query={})

@@ -22,8 +20,6 @@ CLASSES

         user_by_name(self, user_name)

-        sync_ldap_roles(self)
-
         assign_role(self, user_id, role_id)

         unassign_role(self, user_id, role_id)
diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 3751f1f..3476461 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -21,9 +21,3 @@ CLASSES
         update(self, role_id, name, desc)

         available_verbs(self, orgName)
-
-        add_ldap_group(self, role_id, group_name)
-
-        remove_ldap_group(self, role_id, group_name)
-
-        ldap_groups(self, role_id)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index b6809a3..1824ece 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -1,34 +1,6 @@
 MODULE NAME
     katello.client.api.utils

-DESCRIPTION
-    Bunch of api utilities for finding records by their names. Katello API
-    uses integer ids for record identification in most cases. These util
-    functions help with translating names to ids. All of them throw
-    ApiDataError if any of the records is not found.
-
-
-CLASSES
-
-    class ApiDataError(exceptions.Exception)
-        Exception to indicate an error in search for data via api. The only
-        argument is the error message.
-
-        :argument: localized error message
-
-        Inherited methods
-            exceptions.BaseException.__delattr__(...)
-            exceptions.BaseException.__getattribute__(...)
-            exceptions.BaseException.__getitem__(x, y)
-            exceptions.BaseException.__getslice__(x, i, j)
-            exceptions.Exception.__init__(...)
-            exceptions.Exception.__new__(T, S, *...)
-            exceptions.BaseException.__reduce__(...)
-            exceptions.BaseException.__repr__(x)
-            exceptions.BaseException.__setattr__(...)
-            exceptions.BaseException.__setstate__(...)
-            exceptions.BaseException.__str__(x)
-            exceptions.BaseException.__unicode__(...)

 FUNCTIONS

@@ -58,7 +30,3 @@ FUNCTIONS
     get_permission(role_name, permission_name)

     get_filter(org_name, name)
-
-    get_system_group(org_name, system_group_name)
-
-    get_system(org_name, sys_name, env_name=None)


```

katello-cli-headpin-1.1.1-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 96a908a..5d37049 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,7 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name, description=None)
+        create(self, orgName, envId, name, type, description=None)

         update(self, csId, newName, description)

@@ -21,7 +21,7 @@ CLASSES

         dependencies(self, csId)

-        promote(self, csId)
+        apply(self, csId)

         update_content(self, csId, patch)

diff --git a/katello.client.api.user b/katello.client.api.user
index 10a6546..f7ed01a 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,11 +8,13 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, email, disabled, default_environment)
+        create(self, name, pw, email, disabled, default_environment,
+               default_locale=None)

         delete(self, user_id)

-        update(self, user_id, pw, email, disabled, default_environment)
+        update(self, user_id, pw, email, disabled, default_environment,
+               default_locale=None)

         users(self, query={})

diff --git a/katello.client.api.utils b/katello.client.api.utils
index f8cc14a..b6809a3 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -1,12 +1,20 @@
 MODULE NAME
     katello.client.api.utils

+DESCRIPTION
+    Bunch of api utilities for finding records by their names. Katello API
+    uses integer ids for record identification in most cases. These util
+    functions help with translating names to ids. All of them throw
+    ApiDataError if any of the records is not found.
+

 CLASSES

     class ApiDataError(exceptions.Exception)
         Exception to indicate an error in search for data via api. The only
-        argument is [0] the error message.
+        argument is the error message.
+
+        :argument: localized error message

         Inherited methods
             exceptions.BaseException.__delattr__(...)


```

katello-cli-headpin-0.1.20-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index b5b5f7d..305ecc5 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -13,12 +13,19 @@ CLASSES

         activation_key(self, keyId)

-        create(self, envId, name, description, templateId=None)
+        create(self, envId, name, description, usage_limit=-1,
+               templateId=None)

-        update(self, keyId, environmentId, name, description, templateId)
+        update(self, keyId, environmentId, name, description, templateId,
+               usage_limit)

         add_pool(self, keyId, poolid)

         remove_pool(self, keyId, poolid)

         delete(self, keyId)
+
+        add_system_group(self, org_name, activation_key_id, system_group_id)
+
+        remove_system_group(self, org_name, activation_key_id,
+                            system_group_id)
diff --git a/katello.client.api.admin b/katello.client.api.admin
index 368b919..e652a09 100644
--- a/katello.client.api.admin
+++ b/katello.client.api.admin
@@ -8,4 +8,4 @@ CLASSES
         Various administrative actions


-        crl_regen(self, query=None)
+        crl_regen(self, query={})
diff --git a/katello.client.api.system b/katello.client.api.system
index 13d705f..ec9dc2d 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -57,4 +57,8 @@ CLASSES

         report_by_env(self, env_id, format)

+        add_system_groups(self, system_id, system_group_ids)
+
+        remove_system_groups(self, system_id, system_group_ids)
+
         remove_consumer_deletion_record(self, uuid)
diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index ddfed0f..d105f93 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -8,15 +8,15 @@ CLASSES
         Connection class to access environment calls


-        system_groups(self, org_id, query=None)
+        system_groups(self, org_id, query={})

-        system_group(self, org_id, system_group_id, query=None)
+        system_group(self, org_id, system_group_id, query={})

         system_group_history(self, org_id, system_group_id, job_id=None)

-        system_group_by_name(self, org_id, system_group_name)
+        system_group_by_name(self, org_id, system_group_name, query={})

-        system_group_systems(self, org_id, system_group_id, query=None)
+        system_group_systems(self, org_id, system_group_id, query={})

         create(self, org_id, name, description, max_systems)

@@ -44,8 +44,6 @@ CLASSES

         remove_package_groups(self, org_id, system_group_id, packages)

-        errata(self, org_id, system_group_id, type_in=None)
+        errata(self, org_id, system_group_id, type=None)

         install_errata(self, org_id, system_group_id, errata)
-
-        update_systems(self, org_id, system_group_id, env_id, view_id)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 7a0fb62..f8cc14a 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -51,4 +51,6 @@ FUNCTIONS

     get_filter(org_name, name)

+    get_system_group(org_name, system_group_name)
+
     get_system(org_name, sys_name, env_name=None)


```

katello-cli-headpin-0.1.19-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index 07a6679..13d705f 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -56,3 +56,5 @@ CLASSES
         report_by_org(self, orgId, format)

         report_by_env(self, env_id, format)
+
+        remove_consumer_deletion_record(self, uuid)


```

katello-cli-headpin-0.1.18-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index b1a5fbb..07a6679 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access environment calls


-        register(self, name, org, envName, activation_keys, cp_type,
+        register(self, name, org, environment_id, activation_keys, cp_type,
                  release=None, sla=None, facts={})

         unregister(self, system_uuid)
@@ -27,7 +27,7 @@ CLASSES

         system(self, system_id)

-        tasks(self, org_name, env_name, system_name)
+        tasks(self, org_name, environment_id, system_name)

         packages(self, system_id)

@@ -49,7 +49,7 @@ CLASSES

         systems_by_org(self, orgId, query={})

-        systems_by_env(self, orgId, envName, query={})
+        systems_by_env(self, org_name, environment_id, query={})

         errata(self, system_id)

diff --git a/katello.client.api.utils b/katello.client.api.utils
index 1824ece..7a0fb62 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -2,6 +2,26 @@ MODULE NAME
     katello.client.api.utils


+CLASSES
+
+    class ApiDataError(exceptions.Exception)
+        Exception to indicate an error in search for data via api. The only
+        argument is [0] the error message.
+
+        Inherited methods
+            exceptions.BaseException.__delattr__(...)
+            exceptions.BaseException.__getattribute__(...)
+            exceptions.BaseException.__getitem__(x, y)
+            exceptions.BaseException.__getslice__(x, i, j)
+            exceptions.Exception.__init__(...)
+            exceptions.Exception.__new__(T, S, *...)
+            exceptions.BaseException.__reduce__(...)
+            exceptions.BaseException.__repr__(x)
+            exceptions.BaseException.__setattr__(...)
+            exceptions.BaseException.__setstate__(...)
+            exceptions.BaseException.__str__(x)
+            exceptions.BaseException.__unicode__(...)
+
 FUNCTIONS

     get_organization(orgName)
@@ -30,3 +50,5 @@ FUNCTIONS
     get_permission(role_name, permission_name)

     get_filter(org_name, name)
+
+    get_system(org_name, sys_name, env_name=None)


```

katello-cli-headpin-0.1.17-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 352c253..96a908a 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,7 +13,9 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name)
+        create(self, orgName, envId, name, description=None)
+
+        update(self, csId, newName, description)

         delete(self, csId)

diff --git a/katello.client.api.organization b/katello.client.api.organization
index da1a42c..c0902b9 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -18,6 +18,6 @@ CLASSES

         organization(self, name)

-        uebercert(self, name)
+        uebercert(self, name, regenerate=False)

         pools(self, name)
diff --git a/katello.client.api.package b/katello.client.api.package
index f41475f..a45d238 100644
--- a/katello.client.api.package
+++ b/katello.client.api.package
@@ -11,3 +11,5 @@ CLASSES
         package(self, packageId, repoId)

         packages_by_repo(self, repoId)
+
+        search(self, query, repoId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index ff2a1d0..33c01b8 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -27,3 +27,5 @@ CLASSES
         last_sync_status(self, provId)

         import_manifest(self, provId, manifestFile, force=False)
+
+        refresh_products(self, provId)
diff --git a/katello.client.api.system b/katello.client.api.system
index d846476..b1a5fbb 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -8,7 +8,8 @@ CLASSES
         Connection class to access environment calls


-        register(self, name, org, envName, activation_keys, cp_type)
+        register(self, name, org, envName, activation_keys, cp_type,
+                 release=None, sla=None, facts={})

         unregister(self, system_uuid)

@@ -30,6 +31,10 @@ CLASSES

         packages(self, system_id)

+        releases_for_system(self, system_id)
+
+        releases_for_environment(self, env_id)
+
         update(self, system_id, params={})

         install_packages(self, system_id, packages)
@@ -50,4 +55,4 @@ CLASSES

         report_by_org(self, orgId, format)

-        report_by_env(self, orgId, envName, format)
+        report_by_env(self, env_id, format)
diff --git a/katello.client.api.user b/katello.client.api.user
index 882dbe3..10a6546 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,11 +8,11 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, email, disabled)
+        create(self, name, pw, email, disabled, default_environment)

         delete(self, user_id)

-        update(self, user_id, pw, email, disabled)
+        update(self, user_id, pw, email, disabled, default_environment)

         users(self, query={})

@@ -20,6 +20,8 @@ CLASSES

         user_by_name(self, user_name)

+        sync_ldap_roles(self)
+
         assign_role(self, user_id, role_id)

         unassign_role(self, user_id, role_id)
diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 3476461..3751f1f 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -21,3 +21,9 @@ CLASSES
         update(self, role_id, name, desc)

         available_verbs(self, orgName)
+
+        add_ldap_group(self, role_id, group_name)
+
+        remove_ldap_group(self, role_id, group_name)
+
+        ldap_groups(self, role_id)


```

katello-cli-headpin-0.1.16-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index a9f81be..12883de 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -18,23 +18,23 @@ CLASSES

         create(self, provId, name, description, gpgkey)

-        update(self, prodId, description, gpgkey, nogpgkey,
+        update(self, orgName, prodId, description, gpgkey, nogpgkey,
                gpgkey_recursive)

-        show(self, prodId)
+        show(self, orgName, prodId)

-        delete(self, prodId)
+        delete(self, orgName, prodId)

-        sync(self, prodId)
+        sync(self, orgName, prodId)

-        set_sync_plan(self, prodId, planId)
+        set_sync_plan(self, orgName, prodId, planId)

-        remove_sync_plan(self, prodId)
+        remove_sync_plan(self, orgName, prodId)

-        cancel_sync(self, prodId)
+        cancel_sync(self, orgName, prodId)

-        last_sync_status(self, prodId)
+        last_sync_status(self, orgName, prodId)

-        update_filters(self, prodId, filters)
+        update_filters(self, orgName, prodId, filters)

-        filters(self, prodId)
+        filters(self, orgName, prodId)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index dba79c6..5d9d023 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access repositories


-        create(self, prod_id, name, url, gpgkey, nogpgkey)
+        create(self, orgName, prod_id, name, url, gpgkey, nogpgkey)

         update(self, repo_id, gpgkey, nogpgkey)

@@ -17,7 +17,7 @@ CLASSES
         repos_by_env_product(self, envId, productId, name=None,
                              includeDisabled=False)

-        repos_by_product(self, productId, includeDisabled=False)
+        repos_by_product(self, orgName, productId, includeDisabled=False)

         repo(self, repo_id)

@@ -42,3 +42,7 @@ CLASSES
         packagegroupcategories(self, repoid)

         packagegroupcategory_by_id(self, repoid, categoryId)
+
+        update_filters(self, repo_id, filters)
+
+        filters(self, repo_id, inherit=False)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index a2f6d53..1824ece 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -28,3 +28,5 @@ FUNCTIONS
     get_sync_plan(org_name, name)

     get_permission(role_name, permission_name)
+
+    get_filter(org_name, name)


```

katello-cli-headpin-0.1.15-2
=======================

```diff

diff --git a/katello.client.api.environment b/katello.client.api.environment
index cc99ace..6e3b547 100644
--- a/katello.client.api.environment
+++ b/katello.client.api.environment
@@ -14,7 +14,7 @@ CLASSES

         environment_by_name(self, orgId, envName)

-        locker_by_org(self, orgId)
+        library_by_org(self, orgId)

         create(self, orgId, name, description, priorId)

diff --git a/katello.client.api.utils b/katello.client.api.utils
index e1c0dd7..a2f6d53 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -8,7 +8,7 @@ FUNCTIONS

     get_environment(orgName, envName=None)

-    get_locker(orgName)
+    get_library(orgName)

     get_product(orgName, prodName)



```

katello-cli-headpin-0.1.15-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index ef4de05..d846476 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -18,7 +18,11 @@ CLASSES

         available_pools(self, system_id)

-        unsubscribe(self, system_id, pool)
+        unsubscribe(self, system_id, entitlement)
+
+        unsubscribe_by_serial(self, system_id, serial)
+
+        unsubscribe_all(self, system_id)

         system(self, system_id)



```

katello-cli-headpin-0.1.14-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 772b3f2..352c253 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -17,6 +17,8 @@ CLASSES

         delete(self, csId)

+        dependencies(self, csId)
+
         promote(self, csId)

         update_content(self, csId, patch)
diff --git a/katello.client.api.distribution b/katello.client.api.distribution
index b83fde6..d39910d 100644
--- a/katello.client.api.distribution
+++ b/katello.client.api.distribution
@@ -10,4 +10,4 @@ CLASSES

         distributions_by_repo(self, repoId)

-        distribution(self, distribution_id)
+        distribution(self, repoId, distribution_id)
diff --git a/katello.client.api.errata b/katello.client.api.errata
index 6798d51..132bb55 100644
--- a/katello.client.api.errata
+++ b/katello.client.api.errata
@@ -12,4 +12,4 @@ CLASSES

         errata_by_repo(self, repoId, type=None)

-        errata(self, errata_id)
+        errata(self, errata_id, repoId)
diff --git a/katello.client.api.package b/katello.client.api.package
index 99e9978..f41475f 100644
--- a/katello.client.api.package
+++ b/katello.client.api.package
@@ -8,6 +8,6 @@ CLASSES
         Connection class to access package calls


-        package(self, packageId)
+        package(self, packageId, repoId)

         packages_by_repo(self, repoId)
diff --git a/katello.client.api.permission b/katello.client.api.permission
index ae89fcb..4fbf368 100644
--- a/katello.client.api.permission
+++ b/katello.client.api.permission
@@ -8,10 +8,10 @@ CLASSES
         Connection class to access Permissions


-        create(self, roleId, name, description, type_in, verbs, tagIds,
-               orgId=None, all_tags=False)
+        create(self, roleId, name, description, type, verbs, tagIds,
+               orgId=None)

-        permissions(self, roleId, query=None)
+        permissions(self, roleId, query={})

         permission(self, roleId, permissionId)

diff --git a/katello.client.api.product b/katello.client.api.product
index c668ac9..a9f81be 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -16,7 +16,10 @@ CLASSES

         product_by_name(self, orgName, prodName)

-        create(self, provId, name, description)
+        create(self, provId, name, description, gpgkey)
+
+        update(self, prodId, description, gpgkey, nogpgkey,
+               gpgkey_recursive)

         show(self, prodId)

@@ -24,6 +27,10 @@ CLASSES

         sync(self, prodId)

+        set_sync_plan(self, prodId, planId)
+
+        remove_sync_plan(self, prodId)
+
         cancel_sync(self, prodId)

         last_sync_status(self, prodId)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 116a3a8..dba79c6 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,7 +8,9 @@ CLASSES
         Connection class to access repositories


-        create(self, prod_id, name, url)
+        create(self, prod_id, name, url, gpgkey, nogpgkey)
+
+        update(self, repo_id, gpgkey, nogpgkey)

         repos_by_org_env(self, orgName, envId, includeDisabled=False)

diff --git a/katello.client.api.sync_plan b/katello.client.api.sync_plan
index fad7b6c..5f5b8e6 100644
--- a/katello.client.api.sync_plan
+++ b/katello.client.api.sync_plan
@@ -13,7 +13,7 @@ CLASSES
         update(self, org_id, plan_id, name, sync_date, interval,
                description)

-        sync_plans(self, org_id, query=None)
+        sync_plans(self, org_id, query={})

         sync_plan(self, org_id, plan_id)

diff --git a/katello.client.api.system b/katello.client.api.system
index 47100d8..ef4de05 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -22,10 +22,22 @@ CLASSES

         system(self, system_id)

+        tasks(self, org_name, env_name, system_name)
+
         packages(self, system_id)

         update(self, system_id, params={})

+        install_packages(self, system_id, packages)
+
+        remove_packages(self, system_id, packages)
+
+        update_packages(self, system_id, packages)
+
+        install_package_groups(self, system_id, packages)
+
+        remove_package_groups(self, system_id, packages)
+
         systems_by_org(self, orgId, query={})

         systems_by_env(self, orgId, envName, query={})
diff --git a/katello.client.api.task_status b/katello.client.api.task_status
index 65c5bab..89273c1 100644
--- a/katello.client.api.task_status
+++ b/katello.client.api.task_status
@@ -8,3 +8,8 @@ CLASSES


         status(self, taskUuid)
+
+    class SystemTaskStatusAPI(katello.client.api.base.KatelloAPI)
+
+
+        status(self, taskUuid)
diff --git a/katello.client.api.user b/katello.client.api.user
index 0f6d753..882dbe3 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -18,4 +18,12 @@ CLASSES

         user(self, user_id)

+        user_by_name(self, user_name)
+
+        assign_role(self, user_id, role_id)
+
+        unassign_role(self, user_id, role_id)
+
+        roles(self, user_id)
+
         report(self, format)
diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 4e0d0c7..3476461 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -19,3 +19,5 @@ CLASSES
         delete(self, role_id)

         update(self, role_id, name, desc)
+
+        available_verbs(self, orgName)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index e85014a..e1c0dd7 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -20,3 +20,11 @@ FUNCTIONS
     get_template(orgName, envName, tplName)

     get_changeset(orgName, envName, csName)
+
+    get_user(userName)
+
+    get_role(name)
+
+    get_sync_plan(org_name, name)
+
+    get_permission(role_name, permission_name)


```

katello-cli-headpin-0.1.13-1
=======================

```diff

diff --git a/katello.client.api.provider b/katello.client.api.provider
index aa1e4c1..ff2a1d0 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -26,4 +26,4 @@ CLASSES

         last_sync_status(self, provId)

-        import_manifest(self, provId, manifestFile)
+        import_manifest(self, provId, manifestFile, force=False)
diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 1eecbca..4e0d0c7 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -10,7 +10,7 @@ CLASSES

         create(self, name, description)

-        roles(self, query=None)
+        roles(self, query={})

         role(self, role_id)

@@ -19,11 +19,3 @@ CLASSES
         delete(self, role_id)

         update(self, role_id, name, desc)
-
-        available_verbs(self, orgName)
-
-        add_ldap_group(self, role_id, group_name)
-
-        remove_ldap_group(self, role_id, group_name)
-
-        ldap_groups(self, role_id)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index f405cb8..e85014a 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -12,7 +12,8 @@ FUNCTIONS

     get_product(orgName, prodName)

-    get_repo(orgName, prodName, repoName, envName=None)
+    get_repo(orgName, prodName, repoName, envName=None,
+             includeDisabled=False)

     get_provider(orgName, provName)



```

katello-cli-headpin-0.1.12-2
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index b1eb88f..772b3f2 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -20,3 +20,7 @@ CLASSES
         promote(self, csId)

         update_content(self, csId, patch)
+
+        add_content(self, csId, contentType, attrs)
+
+        remove_content(self, csId, contentType, attrs)
diff --git a/katello.client.api.errata b/katello.client.api.errata
index a580160..6798d51 100644
--- a/katello.client.api.errata
+++ b/katello.client.api.errata
@@ -5,9 +5,11 @@ MODULE NAME
 CLASSES

     class ErrataAPI(katello.client.api.base.KatelloAPI)
-        Connection class to access errata calls


-        errata_by_repo(self, repoId)
+        errata_filter(self, repo_id=None, environment_id=None, prod_id=None,
+                      type=None, severity=None)
+
+        errata_by_repo(self, repoId, type=None)

         errata(self, errata_id)
diff --git a/katello.client.api.filter b/katello.client.api.filter
index ffd793e..6dbe013 100644
--- a/katello.client.api.filter
+++ b/katello.client.api.filter
@@ -5,21 +5,15 @@ MODULE NAME
 CLASSES

     class FilterAPI(katello.client.api.base.KatelloAPI)
-        Connection class to access content view filter calls
+        Connection class to access Filter Data


-        filters_by_cvd_and_org(self, def_id, org_id)
+        filters(self, org)

-        get_filter_info(self, filter_name, def_id, org_id)
+        create(self, org, name, description, filter_list)

-        create(self, filter_name, def_id, org_id)
+        delete(self, org, name)

-        delete(self, filter_name, def_id, org_id)
+        info(self, org, name)

-        products(self, filter_name, def_id, org_id)
-
-        update_products(self, filter_name, def_id, org_id, products)
-
-        repos(self, filter_name, def_id, org_id)
-
-        update_repos(self, filter_name, def_id, org_id, repos)
+        update_packages(self, org, name, package_list)
diff --git a/katello.client.api.organization b/katello.client.api.organization
index 80421e5..da1a42c 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -18,6 +18,6 @@ CLASSES

         organization(self, name)

-        generate_uebercert(self, name)
-
         uebercert(self, name)
+
+        pools(self, name)
diff --git a/katello.client.api.product b/katello.client.api.product
index 80244a7..c668ac9 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -18,8 +18,16 @@ CLASSES

         create(self, provId, name, description)

+        show(self, prodId)
+
         delete(self, prodId)

         sync(self, prodId)

+        cancel_sync(self, prodId)
+
         last_sync_status(self, prodId)
+
+        update_filters(self, prodId, filters)
+
+        filters(self, prodId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index beabb27..aa1e4c1 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -5,7 +5,7 @@ MODULE NAME
 CLASSES

     class ProviderAPI(katello.client.api.base.KatelloAPI)
-        Connection class to access repo specific calls
+        Connection class to access provider specific calls


         create(self, name, orgName, description=None, pType=None, url=None)
@@ -22,6 +22,8 @@ CLASSES

         sync(self, provId)

+        cancel_sync(self, provId)
+
         last_sync_status(self, provId)

         import_manifest(self, provId, manifestFile)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 8bc0eb9..116a3a8 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -10,18 +10,23 @@ CLASSES

         create(self, prod_id, name, url)

-        repos_by_org_env(self, orgName, envId)
+        repos_by_org_env(self, orgName, envId, includeDisabled=False)

-        repos_by_env_product(self, envId, productId)
+        repos_by_env_product(self, envId, productId, name=None,
+                             includeDisabled=False)

-        repos_by_product(self, productId)
+        repos_by_product(self, productId, includeDisabled=False)

         repo(self, repo_id)

+        enable(self, repo_id, enable=True)
+
         delete(self, repoId)

         sync(self, repo_id)

+        cancel_sync(self, repo_id)
+
         last_sync_status(self, repo_id)

         repo_discovery(self, org_name, url, repotype)
diff --git a/katello.client.api.system b/katello.client.api.system
index 166b32b..47100d8 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -31,3 +31,7 @@ CLASSES
         systems_by_env(self, orgId, envName, query={})

         errata(self, system_id)
+
+        report_by_org(self, orgId, format)
+
+        report_by_env(self, orgId, envName, format)
diff --git a/katello.client.api.template b/katello.client.api.template
index a5c3e59..e022517 100644
--- a/katello.client.api.template
+++ b/katello.client.api.template
@@ -15,6 +15,10 @@ CLASSES

         import_tpl(self, envId, description, tplFile)

+        validate_tpl(self, tplId, format)
+
+        export_tpl(self, tplId, format)
+
         create(self, envId, name, description, parentId)

         update(self, tplId, newName, description, parentId)
@@ -26,3 +30,7 @@ CLASSES
         promotion_status(self, task_id)

         delete(self, template_id)
+
+        Class variables
+
+            format_content_type = {'json': 'application/json', 'tdl': 'a...
diff --git a/katello.client.api.user b/katello.client.api.user
index 3ba3dbe..0f6d753 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,12 +8,14 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, disabled)
+        create(self, name, pw, email, disabled)

         delete(self, user_id)

-        update(self, user_id, pw, disabled)
+        update(self, user_id, pw, email, disabled)

         users(self, query={})

         user(self, user_id)
+
+        report(self, format)


```

katello-cli-headpin-0.1.11-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index 2f422d3..b5b5f7d 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -11,20 +11,14 @@ CLASSES

         activation_keys_by_environment(self, envId)

-        activation_key(self, orgId, keyId)
+        activation_key(self, keyId)

-        create(self, envId, name, description, usage_limit=-1, view_id=None)
+        create(self, envId, name, description, templateId=None)

-        update(self, orgId, keyId, environmentId, name, description,
-               usage_limit, view_id)
+        update(self, keyId, environmentId, name, description, templateId)

-        add_pool(self, orgId, keyId, poolid)
+        add_pool(self, keyId, poolid)

-        remove_pool(self, orgId, keyId, poolid)
+        remove_pool(self, keyId, poolid)

-        delete(self, orgId, keyId)
-
-        add_system_group(self, org_name, activation_key_id, system_group_id)
-
-        remove_system_group(self, org_name, activation_key_id,
-                            system_group_id)
+        delete(self, keyId)
diff --git a/katello.client.api.base b/katello.client.api.base
index 17707c2..fad59e8 100644
--- a/katello.client.api.base
+++ b/katello.client.api.base
@@ -9,9 +9,9 @@ CLASSES
         after instantiation.


-        __init__(self)
-            x.__init__(...) initializes x; see x.__class__.__doc__ for
-            signature
+        update_dict(self, d, key, value)
+            Update value for key in fictionary only if the value is not
+            None.

         Instance variables

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index be92fe9..b1eb88f 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,18 +13,10 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name, type_in, description=None)
-
-        update(self, csId, newName, description)
+        create(self, orgName, envId, name)

         delete(self, csId)

-        dependencies(self, csId)
-
-        apply(self, csId)
+        promote(self, csId)

         update_content(self, csId, patch)
-
-        add_content(self, csId, contentType, attrs)
-
-        remove_content(self, csId, contentType, attrs)
diff --git a/katello.client.api.distribution b/katello.client.api.distribution
index d39910d..b83fde6 100644
--- a/katello.client.api.distribution
+++ b/katello.client.api.distribution
@@ -10,4 +10,4 @@ CLASSES

         distributions_by_repo(self, repoId)

-        distribution(self, repoId, distribution_id)
+        distribution(self, distribution_id)
diff --git a/katello.client.api.environment b/katello.client.api.environment
index 9823c36..cc99ace 100644
--- a/katello.client.api.environment
+++ b/katello.client.api.environment
@@ -14,9 +14,9 @@ CLASSES

         environment_by_name(self, orgId, envName)

-        library_by_org(self, orgId)
+        locker_by_org(self, orgId)

-        create(self, orgId, name, label, description, priorId)
+        create(self, orgId, name, description, priorId)

         update(self, orgId, envId, name, description, priorId)

diff --git a/katello.client.api.errata b/katello.client.api.errata
index 335b161..a580160 100644
--- a/katello.client.api.errata
+++ b/katello.client.api.errata
@@ -8,9 +8,6 @@ CLASSES
         Connection class to access errata calls


-        errata_filter(self, repo_id=None, environment_id=None, prod_id=None,
-                      type_in=None, severity=None)
+        errata_by_repo(self, repoId)

-        errata_by_repo(self, repoId, type_in=None)
-
-        errata(self, errata_id, repoId)
+        errata(self, errata_id)
diff --git a/katello.client.api.organization b/katello.client.api.organization
index 6940771..80421e5 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access Organization Data


-        create(self, name, label, description)
+        create(self, name, description)

         delete(self, name)

@@ -18,6 +18,6 @@ CLASSES

         organization(self, name)

-        uebercert(self, name, regenerate=False)
+        generate_uebercert(self, name)

-        pools(self, name)
+        uebercert(self, name)
diff --git a/katello.client.api.package b/katello.client.api.package
index a45d238..99e9978 100644
--- a/katello.client.api.package
+++ b/katello.client.api.package
@@ -8,8 +8,6 @@ CLASSES
         Connection class to access package calls


-        package(self, packageId, repoId)
+        package(self, packageId)

         packages_by_repo(self, repoId)
-
-        search(self, query, repoId)
diff --git a/katello.client.api.product b/katello.client.api.product
index a7a2c7a..80244a7 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -14,30 +14,12 @@ CLASSES

         products_by_provider(self, provId, prodName=None)

-        product_by_name_or_label_or_id(self, orgName, prodName, prodLabel,
-                                       prodId)
+        product_by_name(self, orgName, prodName)

-        create(self, provId, name, label, description, gpgkey)
+        create(self, provId, name, description)

-        update(self, orgName, prodId, description, gpgkey, nogpgkey,
-               gpgkey_recursive)
+        delete(self, prodId)

-        show(self, orgName, prodId)
+        sync(self, prodId)

-        delete(self, orgName, prodId)
-
-        sync(self, orgName, prodId)
-
-        set_sync_plan(self, orgName, prodId, planId)
-
-        remove_sync_plan(self, orgName, prodId)
-
-        cancel_sync(self, orgName, prodId)
-
-        last_sync_status(self, orgName, prodId)
-
-        repository_sets(self, orgName, prodId)
-
-        enable_repository_set(self, orgName, prodId, repoSetId)
-
-        disable_repository_set(self, orgName, prodId, repoSetId)
+        last_sync_status(self, prodId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index 67151c6..beabb27 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -5,7 +5,7 @@ MODULE NAME
 CLASSES

     class ProviderAPI(katello.client.api.base.KatelloAPI)
-        Connection class to access provider specific calls
+        Connection class to access repo specific calls


         create(self, name, orgName, description=None, pType=None, url=None)
@@ -22,14 +22,6 @@ CLASSES

         sync(self, provId)

-        cancel_sync(self, provId)
-
         last_sync_status(self, provId)

-        import_manifest(self, provId, manifestFile, force=False)
-
-        delete_manifest(self, provId)
-
-        refresh_products(self, provId)
-
-        repo_discovery(self, provId, url)
+        import_manifest(self, provId, manifestFile)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 18d37f5..8bc0eb9 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,30 +8,26 @@ CLASSES
         Connection class to access repositories


-        create(self, orgName, prod_id, name, label, url, unprotected,
-               gpgkey, nogpgkey)
+        create(self, prod_id, name, url)

-        update(self, repo_id, gpgkey, nogpgkey)
+        repos_by_org_env(self, orgName, envId)

-        repos_by_org_env(self, orgName, envId, includeDisabled=False)
+        repos_by_env_product(self, envId, productId)

-        repos_by_env_product(self, envId, productId, name=None,
-                             includeDisabled=False)
-
-        repos_by_product(self, orgName, productId, includeDisabled=False)
+        repos_by_product(self, productId)

         repo(self, repo_id)

-        enable(self, repo_id, enable=True)
-
         delete(self, repoId)

         sync(self, repo_id)

-        cancel_sync(self, repo_id)
-
         last_sync_status(self, repo_id)

+        repo_discovery(self, org_name, url, repotype)
+
+        repo_discovery_status(self, discoveryTaskId)
+
         packagegroups(self, repoid)

         packagegroup_by_id(self, repoid, groupId)
diff --git a/katello.client.api.system b/katello.client.api.system
index f0d21cc..166b32b 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -8,8 +8,7 @@ CLASSES
         Connection class to access environment calls


-        register(self, name, org, environment_id, activation_keys, cp_type,
-                 release=None, sla=None, facts=None, view_id=None)
+        register(self, name, org, envName, activation_keys, cp_type)

         unregister(self, system_uuid)

@@ -17,50 +16,18 @@ CLASSES

         subscriptions(self, system_id)

-        available_pools(self, system_id, match_system=False,
-                        match_installed=False, no_overlap=False)
+        available_pools(self, system_id)

-        unsubscribe(self, system_id, entitlement)
-
-        unsubscribe_by_serial(self, system_id, serial)
-
-        unsubscribe_all(self, system_id)
+        unsubscribe(self, system_id, pool)

         system(self, system_id)

-        tasks(self, org_name, environment_id, system_name=None,
-              system_uuid=None)
-
         packages(self, system_id)

-        releases_for_system(self, system_id)
-
-        releases_for_environment(self, env_id)
-
-        update(self, system_id, params=None)
-
-        install_packages(self, system_id, packages)
-
-        remove_packages(self, system_id, packages)
-
-        update_packages(self, system_id, packages)
+        update(self, system_id, params={})

-        install_package_groups(self, system_id, packages)
+        systems_by_org(self, orgId, query={})

-        remove_package_groups(self, system_id, packages)
-
-        systems_by_org(self, orgId, query=None)
-
-        systems_by_env(self, environment_id, query=None)
+        systems_by_env(self, orgId, envName, query={})

         errata(self, system_id)
-
-        report_by_org(self, orgId, format_in)
-
-        report_by_env(self, env_id, format_in)
-
-        add_system_groups(self, system_id, system_group_ids)
-
-        remove_system_groups(self, system_id, system_group_ids)
-
-        remove_consumer_deletion_record(self, uuid)
diff --git a/katello.client.api.task_status b/katello.client.api.task_status
index 89273c1..65c5bab 100644
--- a/katello.client.api.task_status
+++ b/katello.client.api.task_status
@@ -8,8 +8,3 @@ CLASSES


         status(self, taskUuid)
-
-    class SystemTaskStatusAPI(katello.client.api.base.KatelloAPI)
-
-
-        status(self, taskUuid)
diff --git a/katello.client.api.template b/katello.client.api.template
index 30d22c0..a5c3e59 100644
--- a/katello.client.api.template
+++ b/katello.client.api.template
@@ -15,10 +15,6 @@ CLASSES

         import_tpl(self, envId, description, tplFile)

-        validate_tpl(self, tplId, format_in)
-
-        export_tpl(self, tplId, format_in)
-
         create(self, envId, name, description, parentId)

         update(self, tplId, newName, description, parentId)
@@ -30,7 +26,3 @@ CLASSES
         promotion_status(self, task_id)

         delete(self, template_id)
-
-        Class variables
-
-            format_content_type = {'json': 'application/json', 'tdl': 'a...
diff --git a/katello.client.api.user b/katello.client.api.user
index 1e390b8..3ba3dbe 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,26 +8,12 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, email, disabled, default_environment,
-               default_locale=None)
+        create(self, name, pw, disabled)

         delete(self, user_id)

-        update(self, user_id, pw, email, disabled, default_environment,
-               default_locale=None)
+        update(self, user_id, pw, disabled)

-        users(self, query=None)
+        users(self, query={})

         user(self, user_id)
-
-        user_by_name(self, user_name)
-
-        sync_ldap_roles(self)
-
-        assign_role(self, user_id, role_id)
-
-        unassign_role(self, user_id, role_id)
-
-        roles(self, user_id)
-
-        report(self, format_in)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index a1bce7f..f405cb8 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -1,34 +1,6 @@
 MODULE NAME
     katello.client.api.utils

-DESCRIPTION
-    Bunch of api utilities for finding records by their names. Katello API
-    uses integer ids for record identification in most cases. These util
-    functions help with translating names to ids. All of them throw
-    ApiDataError if any of the records is not found.
-
-
-CLASSES
-
-    class ApiDataError(exceptions.Exception)
-        Exception to indicate an error in search for data via api. The only
-        argument is the error message.
-
-        :argument: localized error message
-
-        Inherited methods
-            exceptions.BaseException.__delattr__(...)
-            exceptions.BaseException.__getattribute__(...)
-            exceptions.BaseException.__getitem__(x, y)
-            exceptions.BaseException.__getslice__(x, i, j)
-            exceptions.Exception.__init__(...)
-            exceptions.Exception.__new__(T, S, *...)
-            exceptions.BaseException.__reduce__(...)
-            exceptions.BaseException.__repr__(x)
-            exceptions.BaseException.__setattr__(...)
-            exceptions.BaseException.__setstate__(...)
-            exceptions.BaseException.__str__(x)
-            exceptions.BaseException.__unicode__(...)

 FUNCTIONS

@@ -36,33 +8,14 @@ FUNCTIONS

     get_environment(orgName, envName=None)

-    get_library(orgName)
-
-    get_product(orgName, prodName=None, prodLabel=None, prodId=None)
-        Retrieve product by name, label or id.
+    get_locker(orgName)

-    get_content_view(org_name, view_label=None, view_name=None,
-                     view_id=None)
+    get_product(orgName, prodName)

-    get_cv_definition(org_name, def_label=None, def_name=None, def_id=None)
-
-    get_repo(orgName, repoName, prodName=None, prodLabel=None, prodId=None,
-             envName=None, includeDisabled=False)
+    get_repo(orgName, prodName, repoName, envName=None)

     get_provider(orgName, provName)

-    get_changeset(orgName, envName, csName)
+    get_template(orgName, envName, tplName)

-    get_user(userName)
-
-    get_role(name)
-
-    get_sync_plan(org_name, name)
-
-    get_permission(role_name, permission_name)
-
-    get_system_group(org_name, system_group_name)
-
-    get_system(org_name, sys_name, env_name=None, sys_uuid=None)
-
-    get_distributor(org_name, dist_name, env_name=None, dist_uuid=None)
+    get_changeset(orgName, envName, csName)


```

katello-cli-1.3.6-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index f93efe4..2f422d3 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -13,11 +13,10 @@ CLASSES

         activation_key(self, orgId, keyId)

-        create(self, envId, name, description, usage_limit=-1,
-               templateId=None, view_id=None)
+        create(self, envId, name, description, usage_limit=-1, view_id=None)

         update(self, orgId, keyId, environmentId, name, description,
-               templateId, usage_limit, view_id)
+               usage_limit, view_id)

         add_pool(self, orgId, keyId, poolid)

diff --git a/katello.client.api.content_view_definition b/katello.client.api.content_view_definition
index 6d41f3d..ba998dd 100644
--- a/katello.client.api.content_view_definition
+++ b/katello.client.api.content_view_definition
@@ -15,7 +15,7 @@ CLASSES

         show(self, org_id, cvd_id)

-        create(self, org_id, name, label, description)
+        create(self, org_id, name, label, description, composite=False)

         update(self, org, cvd_id, name, description)

@@ -23,6 +23,8 @@ CLASSES

         publish(self, org_id, cvd_id, name, label=None, description=None)

+        clone(self, org, cvd_id, name, label=None, description=None)
+
         products(self, org, cvd_id)

         update_products(self, org, cvd, products)
diff --git a/katello.client.api.distributor b/katello.client.api.distributor
new file mode 100644
index 0000000..8d57d28
--- /dev/null
+++ b/katello.client.api.distributor
@@ -0,0 +1,35 @@
+MODULE NAME
+    katello.client.api.distributor
+
+
+CLASSES
+
+    class DistributorAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access environment calls
+
+
+        create(self, name, org, environment_id, cp_type='candlepin')
+
+        delete(self, distributor_uuid)
+
+        subscribe(self, distributor_id, pool, quantity)
+
+        subscriptions(self, distributor_id)
+
+        available_pools(self, distributor_id)
+
+        unsubscribe(self, distributor_id, entitlement)
+
+        unsubscribe_by_serial(self, distributor_id, serial)
+
+        unsubscribe_all(self, distributor_id)
+
+        distributor(self, distributor_id)
+
+        update(self, distributor_id, params=None)
+
+        distributor_by_name(self, orgName, distName)
+
+        distributors_by_org(self, orgId, query=None)
+
+        distributors_by_env(self, environment_id, query=None)
diff --git a/katello.client.api.filter b/katello.client.api.filter
index 58ca285..ffd793e 100644
--- a/katello.client.api.filter
+++ b/katello.client.api.filter
@@ -5,17 +5,21 @@ MODULE NAME
 CLASSES

     class FilterAPI(katello.client.api.base.KatelloAPI)
-        Connection class to access Filter Data
+        Connection class to access content view filter calls


-        filters(self, org)
+        filters_by_cvd_and_org(self, def_id, org_id)

-        create(self, org, name, description, filter_list)
+        get_filter_info(self, filter_name, def_id, org_id)

-        update(self, org, filter_id, new_name)
+        create(self, filter_name, def_id, org_id)

-        delete(self, org, name)
+        delete(self, filter_name, def_id, org_id)

-        info(self, org, name)
+        products(self, filter_name, def_id, org_id)

-        update_packages(self, org, name, package_list)
+        update_products(self, filter_name, def_id, org_id, products)
+
+        repos(self, filter_name, def_id, org_id)
+
+        update_repos(self, filter_name, def_id, org_id, repos)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index b5c4c91..18d37f5 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,7 +8,8 @@ CLASSES
         Connection class to access repositories


-        create(self, orgName, prod_id, name, label, url, gpgkey, nogpgkey)
+        create(self, orgName, prod_id, name, label, url, unprotected,
+               gpgkey, nogpgkey)

         update(self, repo_id, gpgkey, nogpgkey)

diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index a9dd56d..ddfed0f 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -47,3 +47,5 @@ CLASSES
         errata(self, org_id, system_group_id, type_in=None)

         install_errata(self, org_id, system_group_id, errata)
+
+        update_systems(self, org_id, system_group_id, env_id, view_id)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 0cae360..a1bce7f 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -51,8 +51,6 @@ FUNCTIONS

     get_provider(orgName, provName)

-    get_template(orgName, envName, tplName)
-
     get_changeset(orgName, envName, csName)

     get_user(userName)
@@ -66,3 +64,5 @@ FUNCTIONS
     get_system_group(org_name, system_group_name)

     get_system(org_name, sys_name, env_name=None, sys_uuid=None)
+
+    get_distributor(org_name, dist_name, env_name=None, dist_uuid=None)


```

katello-cli-1.3.5-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index 533a822..f93efe4 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -14,10 +14,10 @@ CLASSES
         activation_key(self, orgId, keyId)

         create(self, envId, name, description, usage_limit=-1,
-               templateId=None)
+               templateId=None, view_id=None)

         update(self, orgId, keyId, environmentId, name, description,
-               templateId, usage_limit)
+               templateId, usage_limit, view_id)

         add_pool(self, orgId, keyId, poolid)

diff --git a/katello.client.api.content_view_definition b/katello.client.api.content_view_definition
index ba998dd..6d41f3d 100644
--- a/katello.client.api.content_view_definition
+++ b/katello.client.api.content_view_definition
@@ -15,7 +15,7 @@ CLASSES

         show(self, org_id, cvd_id)

-        create(self, org_id, name, label, description, composite=False)
+        create(self, org_id, name, label, description)

         update(self, org, cvd_id, name, description)

@@ -23,8 +23,6 @@ CLASSES

         publish(self, org_id, cvd_id, name, label=None, description=None)

-        clone(self, org, cvd_id, name, label=None, description=None)
-
         products(self, org, cvd_id)

         update_products(self, org, cvd, products)
diff --git a/katello.client.api.hardware_model b/katello.client.api.hardware_model
new file mode 100644
index 0000000..52adb60
--- /dev/null
+++ b/katello.client.api.hardware_model
@@ -0,0 +1,43 @@
+MODULE NAME
+    katello.client.api.hardware_model
+
+
+CLASSES
+
+    class HardwareModelAPI(katello.client.api.base.KatelloAPI)
+
+
+        list(self)
+            list hardware model
+
+        show(self, model_id)
+            show hardware model
+
+            :type  model_id: string :param model_id: hardware model name
+
+        create(self, data)
+            create hardware model
+
+            :type  data['name']: string :param data['name']: :type
+            data['info']: string :param data['info']: :type
+            data['vendor_class']: string :param data['vendor_class']: :type
+            data['hardware_model']: string :param data['hardware_model']:
+
+        update(self, model_id, data)
+            update hardware model
+
+            :type  model_id: string :param model_id: hardware model name
+            :type  data: hash :param data: hardware model info :type
+            data['name']: string :param data['name']: :type  data['info']:
+            string :param data['info']: :type  data['vendor_class']: string
+            :param data['vendor_class']: :type  data['hardware_model']:
+            string :param data['hardware_model']:
+
+        destroy(self, model_id)
+            destroy hardware model
+
+            :type  model_id: string :param model_id: hardware model name
+
+        Class methods
+
+            __path(cls, model_id=None)
diff --git a/katello.client.api.organization_default_info b/katello.client.api.organization_default_info
new file mode 100644
index 0000000..fa0c778
--- /dev/null
+++ b/katello.client.api.organization_default_info
@@ -0,0 +1,17 @@
+MODULE NAME
+    katello.client.api.organization_default_info
+
+
+CLASSES
+
+    class OrganizationDefaultInfoAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access default info keys under an organization
+
+
+        create(self, org_name, informable_type, keyname)
+
+        index(self, org_name, informable_type)
+
+        destroy(self, org_name, informable_type, keyname)
+
+        apply(self, org_name, informable_type)
diff --git a/katello.client.api.product b/katello.client.api.product
index 6e17144..a7a2c7a 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -35,3 +35,9 @@ CLASSES
         cancel_sync(self, orgName, prodId)

         last_sync_status(self, orgName, prodId)
+
+        repository_sets(self, orgName, prodId)
+
+        enable_repository_set(self, orgName, prodId, repoSetId)
+
+        disable_repository_set(self, orgName, prodId, repoSetId)
diff --git a/katello.client.api.system b/katello.client.api.system
index 688e3cf..f0d21cc 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -9,7 +9,7 @@ CLASSES


         register(self, name, org, environment_id, activation_keys, cp_type,
-                 release=None, sla=None, facts=None)
+                 release=None, sla=None, facts=None, view_id=None)

         unregister(self, system_uuid)

diff --git a/katello.client.api.utils b/katello.client.api.utils
index 7a24b35..0cae360 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -41,8 +41,13 @@ FUNCTIONS
     get_product(orgName, prodName=None, prodLabel=None, prodId=None)
         Retrieve product by name, label or id.

-    get_repo(orgName, prodName, prodLabel, prodId, repoName, envName=None,
-             includeDisabled=False)
+    get_content_view(org_name, view_label=None, view_name=None,
+                     view_id=None)
+
+    get_cv_definition(org_name, def_label=None, def_name=None, def_id=None)
+
+    get_repo(orgName, repoName, prodName=None, prodLabel=None, prodId=None,
+             envName=None, includeDisabled=False)

     get_provider(orgName, provName)



```

katello-cli-1.3.4-1
=======================

```diff

diff --git a/katello.client.api.compute_resource b/katello.client.api.compute_resource
new file mode 100644
index 0000000..7fb11a8
--- /dev/null
+++ b/katello.client.api.compute_resource
@@ -0,0 +1,81 @@
+MODULE NAME
+    katello.client.api.compute_resource
+
+
+CLASSES
+
+    class ComputeResourceAPI(katello.client.api.base.KatelloAPI)
+
+
+        index(self, queries=None)
+            :type queries: dict :param queries: queries for filtering
+            compute resources
+
+        show(self, resource_id)
+            :type resource_id: string :param resource_id: compute resource
+            identifier
+
+        create(self, data)
+            :type name: string
+
+            :type provider: string :param provider: Provider type, one of:
+            Libvirt Ovirt EC2 Vmware Openstack Rackspace
+
+            :type url: string :param url: URL for Libvirt, Ovirt, and
+            Openstack
+
+            :type description: string
+
+            :type user: string :param user: Username for Ovirt, EC2,
+            Vmware, Openstack. Access Key for EC2.
+
+            :type password: string :param password: Password for Ovirt,
+            EC2, Vmware, Openstack. Secret key for EC2
+
+            :type uuid: string :param uuid: for Ovirt, Vmware Datacenter
+
+            :type region: string :param region: for EC2 only
+
+            :type tenant: string :param tenant: for Openstack only
+
+            :type server: string :param server: for Vmware
+
+        update(self, resource_id, data)
+            :type resource_id: string :param resource_id: compute resource
+            identifier
+
+            :type data['name']: string
+
+            :type data['provider']: string :param data['provider']:
+            Provider type, one of: Libvirt Ovirt EC2 Vmware Openstack
+            Rackspace
+
+            :type data['url']: string :param data['url']: URL for Libvirt,
+            Ovirt, and Openstack
+
+            :type data['description']: string
+
+            :type data['user']: string :param data['user']: Username for
+            Ovirt, EC2, Vmware, Openstack. Access Key for EC2.
+
+            :type data['password']: string :param data['password']:
+            Password for Ovirt, EC2, Vmware, Openstack. Secret key for EC2
+
+            :type data['uuid']: string :param data['uuid']: for Ovirt,
+            Vmware Datacenter
+
+            :type data['region']: string :param data['region']: for EC2
+            only
+
+            :type data['tenant']: string :param data['tenant']: for
+            Openstack only
+
+            :type data['server']: string :param data['server']: for Vmware
+
+        destroy(self, resource_id)
+            :type resource_id: string :param resource_id: resource
+            identifier
+
+        Class methods
+
+            __path(cls, resource_id=None)
diff --git a/katello.client.api.product b/katello.client.api.product
index 7bd14e6..6e17144 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -35,7 +35,3 @@ CLASSES
         cancel_sync(self, orgName, prodId)

         last_sync_status(self, orgName, prodId)
-
-        update_filters(self, orgName, prodId, filters)
-
-        filters(self, orgName, prodId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index 9db8ecf..67151c6 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -31,3 +31,5 @@ CLASSES
         delete_manifest(self, provId)

         refresh_products(self, provId)
+
+        repo_discovery(self, provId, url)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 579c917..b5c4c91 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -31,10 +31,6 @@ CLASSES

         last_sync_status(self, repo_id)

-        repo_discovery(self, org_name, url, repotype)
-
-        repo_discovery_status(self, discoveryTaskId)
-
         packagegroups(self, repoid)

         packagegroup_by_id(self, repoid, groupId)
@@ -42,7 +38,3 @@ CLASSES
         packagegroupcategories(self, repoid)

         packagegroupcategory_by_id(self, repoid, categoryId)
-
-        update_filters(self, repo_id, filters)
-
-        filters(self, repo_id, inherit=False)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 786e104..7a24b35 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -58,8 +58,6 @@ FUNCTIONS

     get_permission(role_name, permission_name)

-    get_filter(org_name, name)
-
     get_system_group(org_name, system_group_name)

     get_system(org_name, sys_name, env_name=None, sys_uuid=None)


```

katello-cli-1.3.2-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index 6e17144..7bd14e6 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -35,3 +35,7 @@ CLASSES
         cancel_sync(self, orgName, prodId)

         last_sync_status(self, orgName, prodId)
+
+        update_filters(self, orgName, prodId, filters)
+
+        filters(self, orgName, prodId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index 67151c6..9db8ecf 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -31,5 +31,3 @@ CLASSES
         delete_manifest(self, provId)

         refresh_products(self, provId)
-
-        repo_discovery(self, provId, url)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index b5c4c91..579c917 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -31,6 +31,10 @@ CLASSES

         last_sync_status(self, repo_id)

+        repo_discovery(self, org_name, url, repotype)
+
+        repo_discovery_status(self, discoveryTaskId)
+
         packagegroups(self, repoid)

         packagegroup_by_id(self, repoid, groupId)
@@ -38,3 +42,7 @@ CLASSES
         packagegroupcategories(self, repoid)

         packagegroupcategory_by_id(self, repoid, categoryId)
+
+        update_filters(self, repo_id, filters)
+
+        filters(self, repo_id, inherit=False)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 7a24b35..786e104 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -58,6 +58,8 @@ FUNCTIONS

     get_permission(role_name, permission_name)

+    get_filter(org_name, name)
+
     get_system_group(org_name, system_group_name)

     get_system(org_name, sys_name, env_name=None, sys_uuid=None)


```

katello-cli-1.3.1_pulpv2-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index 7bd14e6..6e17144 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -35,7 +35,3 @@ CLASSES
         cancel_sync(self, orgName, prodId)

         last_sync_status(self, orgName, prodId)
-
-        update_filters(self, orgName, prodId, filters)
-
-        filters(self, orgName, prodId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index 9db8ecf..67151c6 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -31,3 +31,5 @@ CLASSES
         delete_manifest(self, provId)

         refresh_products(self, provId)
+
+        repo_discovery(self, provId, url)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 579c917..b5c4c91 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -31,10 +31,6 @@ CLASSES

         last_sync_status(self, repo_id)

-        repo_discovery(self, org_name, url, repotype)
-
-        repo_discovery_status(self, discoveryTaskId)
-
         packagegroups(self, repoid)

         packagegroup_by_id(self, repoid, groupId)
@@ -42,7 +38,3 @@ CLASSES
         packagegroupcategories(self, repoid)

         packagegroupcategory_by_id(self, repoid, categoryId)
-
-        update_filters(self, repo_id, filters)
-
-        filters(self, repo_id, inherit=False)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 786e104..7a24b35 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -58,8 +58,6 @@ FUNCTIONS

     get_permission(role_name, permission_name)

-    get_filter(org_name, name)
-
     get_system_group(org_name, system_group_name)

     get_system(org_name, sys_name, env_name=None, sys_uuid=None)


```

katello-cli-1.3.1-1
=======================

```diff

diff --git a/katello.client.api.smart_proxy b/katello.client.api.smart_proxy
new file mode 100644
index 0000000..8cc88a1
--- /dev/null
+++ b/katello.client.api.smart_proxy
@@ -0,0 +1,51 @@
+MODULE NAME
+    katello.client.api.smart_proxy
+
+
+CLASSES
+
+    class SmartProxyAPI(katello.client.api.base.KatelloAPI)
+
+
+        list(self, queries=None)
+            list smart proxy
+
+            :type  data['search']: string :param data['search']: Filter
+            results
+
+            :type  data['order']: string :param data['order']: Sort results
+
+        show(self, proxy_id, queries=None)
+            show smart proxy
+
+            :type  data['proxy_id']: string :param data['proxy_id']: domain
+            name (no slashes)
+
+        create(self, data)
+            The fullname field is used for human readability in reports and
+            other pages that refer to domains, and also available as an
+            external node parameter
+
+            :type  data['name']: string :param data['name']: The smart
+            proxy name
+
+            :type  data['url']: string :param data['url']: The smart proxy
+            URL starting with 'http://' or 'https://'
+
+        update(self, proxy_id, data)
+            update smart proxy
+
+            :type  data['proxy_id']: string :param data['proxy_id']: domain
+            name (no slashes)
+
+            :type  data['name']: string :param data['name']: The smart
+            proxy name
+
+            :type  data['url']: string :param data['url']: The smart proxy
+            URL starting with 'http://' or 'https://'
+
+        destroy(self, proxy_id)
+            destroy smart proxy
+
+            :type  data['proxy_id']: string :param data['proxy_id']: domain
+            name (no slashes)


```

katello-cli-1.2.2-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index 12bbbee..7bd14e6 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -14,7 +14,8 @@ CLASSES

         products_by_provider(self, provId, prodName=None)

-        product_by_name(self, orgName, prodName)
+        product_by_name_or_label_or_id(self, orgName, prodName, prodLabel,
+                                       prodId)

         create(self, provId, name, label, description, gpgkey)

diff --git a/katello.client.api.subnet b/katello.client.api.subnet
new file mode 100644
index 0000000..6e2d589
--- /dev/null
+++ b/katello.client.api.subnet
@@ -0,0 +1,116 @@
+MODULE NAME
+    katello.client.api.subnet
+
+
+CLASSES
+
+    class SubnetAPI(katello.client.api.base.KatelloAPI)
+
+
+        get(self, subnet_id)
+            Get a subnet
+
+            :type  subnet_id: string :param subnet_id: <p>Subnet numeric
+            identifier</p>
+
+        list(self, queries=None)
+            List subnets
+
+            :type  data['search']: string :param data['search']: <p>Filter
+            results</p>
+
+            :type  data['order']: string :param data['order']: <p>Sort
+            results</p>
+
+        create(self, data)
+            Create a subnet
+
+            :type  data['name']: string :param data['name']: <p>Subnet
+            name</p>
+
+            :type  data['network']: string :param data['network']:
+            <p>Subnet network</p>
+
+            :type  data['mask']: string :param data['mask']: <p>Netmask for
+            this subnet</p>
+
+            :type  data['gateway']: string :param data['gateway']:
+            <p>Primary DNS for this subnet</p>
+
+            :type  data['dns_primary']: string :param data['dns_primary']:
+            <p>Primary DNS for this subnet</p>
+
+            :type  data['dns_secondary']: string :param
+            data['dns_secondary']: <p>Secondary DNS for this subnet</p>
+
+            :type  data['from']: string :param data['from']: <p>Starting IP
+            Address for IP auto suggestion</p>
+
+            :type  data['to']: string :param data['to']: <p>Ending IP
+            Address for IP auto suggestion</p>
+
+            :type  data['vlanid']: string :param data['vlanid']: <p>VLAN ID
+            for this subnet</p>
+
+            :type  data['domain_ids']: string :param data['domain_ids']:
+            <p>Domains in which this subnet is part</p>
+
+            :type  data['dhcp_id']: string :param data['dhcp_id']: <p>DHCP
+            Proxy to use within this subnet</p>
+
+            :type  data['tftp_id']: string :param data['tftp_id']: <p>TFTP
+            Proxy to use within this subnet</p>
+
+            :type  data['dns_id']: string :param data['dns_id']: <p>DNS
+            Proxy to use within this subnet</p>
+
+        update(self, subnet_id, data)
+            Update a subnet
+
+            :type  subnet_id: string :param subnet_id: <p>Subnet numeric
+            identifier</p>
+
+            :type  data['name']: string :param data['name']: <p>Subnet
+            name</p>
+
+            :type  data['network']: string :param data['network']:
+            <p>Subnet network</p>
+
+            :type  data['mask']: string :param data['mask']: <p>Netmask for
+            this subnet</p>
+
+            :type  data['gateway']: string :param data['gateway']:
+            <p>Primary DNS for this subnet</p>
+
+            :type  data['dns_primary']: string :param data['dns_primary']:
+            <p>Primary DNS for this subnet</p>
+
+            :type  data['dns_secondary']: string :param
+            data['dns_secondary']: <p>Secondary DNS for this subnet</p>
+
+            :type  data['from']: string :param data['from']: <p>Starting IP
+            Address for IP auto suggestion</p>
+
+            :type  data['to']: string :param data['to']: <p>Ending IP
+            Address for IP auto suggestion</p>
+
+            :type  data['vlanid']: string :param data['vlanid']: <p>VLAN ID
+            for this subnet</p>
+
+            :type  data['domain_ids']: string :param data['domain_ids']:
+            <p>Domains in which this subnet is part</p>
+
+            :type  data['dhcp_id']: string :param data['dhcp_id']: <p>DHCP
+            Proxy to use within this subnet</p>
+
+            :type  data['tftp_id']: string :param data['tftp_id']: <p>TFTP
+            Proxy to use within this subnet</p>
+
+            :type  data['dns_id']: string :param data['dns_id']: <p>DNS
+            Proxy to use within this subnet</p>
+
+        destroy(self, subnet_id)
+            Destroy a subnet
+
+            :type  subnet_id: string :param subnet_id: <p>Subnet numeric
+            identifier</p>
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 96b7bc5..786e104 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -38,9 +38,10 @@ FUNCTIONS

     get_library(orgName)

-    get_product(orgName, prodName)
+    get_product(orgName, prodName=None, prodLabel=None, prodId=None)
+        Retrieve product by name, label or id.

-    get_repo(orgName, prodName, repoName, envName=None,
+    get_repo(orgName, prodName, prodLabel, prodId, repoName, envName=None,
              includeDisabled=False)

     get_provider(orgName, provName)


```

katello-cli-1.2.1-1
=======================

```diff

diff --git a/katello.client.api.architecture b/katello.client.api.architecture
new file mode 100644
index 0000000..37dc66a
--- /dev/null
+++ b/katello.client.api.architecture
@@ -0,0 +1,25 @@
+MODULE NAME
+    katello.client.api.architecture
+
+
+CLASSES
+
+    class ArchitectureAPI(katello.client.api.base.KatelloAPI)
+
+
+        index(self, queries=None)
+            :type queries: dict :param queries: queries for filtering the
+            architectures
+
+        show(self, arch_id)
+            :type arch_id: string :param arch_id: architecture identifier
+
+        create(self, data)
+            :type data['name']: string
+
+        update(self, arch_id, data)
+            :type arch_id: string :param arch_id: architecture identifier
+            :type data['name']: string
+
+        destroy(self, arch_id)
+            :type arch_id: string :param arch_id: architecture identifier
diff --git a/katello.client.api.config_template b/katello.client.api.config_template
new file mode 100644
index 0000000..44d74eb
--- /dev/null
+++ b/katello.client.api.config_template
@@ -0,0 +1,60 @@
+MODULE NAME
+    katello.client.api.config_template
+
+
+CLASSES
+
+    class ConfigTemplateAPI(katello.client.api.base.KatelloAPI)
+
+
+        list(self, queries)
+            list config template
+
+            :type  queries['search']: string :param queries['search']:
+            filter results
+
+            :type  queries['order']: string :param queries['order']: sort
+            results
+
+        show(self, tpl_id)
+            show config template
+
+        create(self, data)
+            create config template
+
+            :type  data['name']: string :param data['name']: template name
+
+            :type  data['template']: string :param data['template']: :type
+            data['snippet']: string :param data['snippet']: :type
+            data['audit_comment']: string
+
+        update(self, tpl_id, data)
+            update config template
+
+            :type  data['name']: string :param data['name']: template name
+
+            :type  data['template']: string :param data['template']: :type
+            data['snippet']: string :param data['snippet']: :type
+            data['audit_comment']: string :param data['audit_comment']:
+            :type  data['template_kind_id']: string :param
+            data['template_kind_id']: not relevant for snippet
+
+            :type  data['template_combinations_attributes']: string :param
+            data['template_combinations_attributes']: Array of template
+            combinations (hostgroup_id, environment_id)
+
+            :type  data['operatingsystem_ids']: string :param
+            data['operatingsystem_ids']: Array of operating systems ID to
+            associate the template with
+
+        revision(self, queries)
+            revision config template
+
+            :type  data['version']: string :param data['version']: template
+            version
+
+        destroy(self, tpl_id)
+            destroy config template
+
+        build_pxe_default(self)
+            build_pxe_default config template
diff --git a/katello.client.api.custom_info b/katello.client.api.custom_info
index 5e0cf10..34d786a 100644
--- a/katello.client.api.custom_info
+++ b/katello.client.api.custom_info
@@ -14,7 +14,6 @@ CLASSES
         get_custom_info(self, informable_type, informable_id, keyname=None)

         update_custom_info(self, informable_type, informable_id, keyname,
-                           current_value, value)
+                           new_value)

-        remove_custom_info(self, informable_type, informable_id,
-                           keyname=None, value=None)
+        remove_custom_info(self, informable_type, informable_id, keyname)
diff --git a/katello.client.api.domain b/katello.client.api.domain
new file mode 100644
index 0000000..f51766a
--- /dev/null
+++ b/katello.client.api.domain
@@ -0,0 +1,61 @@
+MODULE NAME
+    katello.client.api.domain
+
+
+CLASSES
+
+    class DomainAPI(katello.client.api.base.KatelloAPI)
+
+
+        list(self, queries)
+            list domain
+
+            :type  queries['search']: string :param queries['search']:
+            Filter results
+
+            :type  queries['order']: string :param queries['order']: Sort
+            results
+
+        show(self, domain_id)
+            show domain
+
+            :type domain_id: string
+
+        create(self, data)
+            The fullname< field is used for human readability in reports
+            and other pages that refer to domains, and also available as an
+            external node parameter
+
+            :type  data['name']: string :param data['name']: The full DNS
+            Domain name
+
+            :type  data['fullname']: string :param data['fullname']: Full
+            name describing the domain
+
+            :type  data['dns_id']: string :param data['dns_id']: DNS Proxy
+            to use within this domain
+
+            :type  data['domain_parameters_attributes']: string :param
+            data['domain_parameters_attributes']: Array of parameters
+            (name, value)
+
+        update(self, domain_id, data)
+            update domain
+
+            :type  domain_id: string
+
+            :type  data['name']: string :param data['name']: The full DNS
+            Domain name
+
+            :type  data['fullname']: string :param data['fullname']: Full
+            name describing the domain
+
+            :type  data['dns_id']: string :param data['dns_id']: DNS Proxy
+            to use within this domain
+
+            :type  data['domain_parameters_attributes']: string :param
+            data['domain_parameters_attributes']: Array of parameters
+            (name, value)
+
+        destroy(self, domain_id)
+            destroy domain
diff --git a/katello.client.api.organization_system_info_keys b/katello.client.api.organization_system_info_keys
new file mode 100644
index 0000000..8668ef5
--- /dev/null
+++ b/katello.client.api.organization_system_info_keys
@@ -0,0 +1,18 @@
+MODULE NAME
+    katello.client.api.organization_system_info_keys
+
+
+CLASSES
+
+    class OrganizationSystemInfoKeysAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access default system info keys under an
+        organization
+
+
+        create(self, org_name, keyname)
+
+        index(self, org_name)
+
+        destroy(self, org_name, keyname)
+
+        apply(self, org_name)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index 33c01b8..9db8ecf 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -28,4 +28,6 @@ CLASSES

         import_manifest(self, provId, manifestFile, force=False)

+        delete_manifest(self, provId)
+
         refresh_products(self, provId)


```

katello-cli-1.1.9-1
=======================

```diff

diff --git a/katello.client.api.custom_info b/katello.client.api.custom_info
new file mode 100644
index 0000000..5e0cf10
--- /dev/null
+++ b/katello.client.api.custom_info
@@ -0,0 +1,20 @@
+MODULE NAME
+    katello.client.api.custom_info
+
+
+CLASSES
+
+    class CustomInfoAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access custom info calls
+
+
+        add_custom_info(self, informable_type, informable_id, keyname,
+                        value)
+
+        get_custom_info(self, informable_type, informable_id, keyname=None)
+
+        update_custom_info(self, informable_type, informable_id, keyname,
+                           current_value, value)
+
+        remove_custom_info(self, informable_type, informable_id,
+                           keyname=None, value=None)
diff --git a/katello.client.api.environment b/katello.client.api.environment
index 6e3b547..9823c36 100644
--- a/katello.client.api.environment
+++ b/katello.client.api.environment
@@ -16,7 +16,7 @@ CLASSES

         library_by_org(self, orgId)

-        create(self, orgId, name, description, priorId)
+        create(self, orgId, name, label, description, priorId)

         update(self, orgId, envId, name, description, priorId)

diff --git a/katello.client.api.filter b/katello.client.api.filter
index 6dbe013..58ca285 100644
--- a/katello.client.api.filter
+++ b/katello.client.api.filter
@@ -12,6 +12,8 @@ CLASSES

         create(self, org, name, description, filter_list)

+        update(self, org, filter_id, new_name)
+
         delete(self, org, name)

         info(self, org, name)
diff --git a/katello.client.api.organization b/katello.client.api.organization
index c0902b9..6940771 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access Organization Data


-        create(self, name, description)
+        create(self, name, label, description)

         delete(self, name)

diff --git a/katello.client.api.product b/katello.client.api.product
index 12883de..12bbbee 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -16,7 +16,7 @@ CLASSES

         product_by_name(self, orgName, prodName)

-        create(self, provId, name, description, gpgkey)
+        create(self, provId, name, label, description, gpgkey)

         update(self, orgName, prodId, description, gpgkey, nogpgkey,
                gpgkey_recursive)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 5d9d023..579c917 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access repositories


-        create(self, orgName, prod_id, name, url, gpgkey, nogpgkey)
+        create(self, orgName, prod_id, name, label, url, gpgkey, nogpgkey)

         update(self, repo_id, gpgkey, nogpgkey)



```

katello-cli-1.1.8-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index 305ecc5..533a822 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -11,19 +11,19 @@ CLASSES

         activation_keys_by_environment(self, envId)

-        activation_key(self, keyId)
+        activation_key(self, orgId, keyId)

         create(self, envId, name, description, usage_limit=-1,
                templateId=None)

-        update(self, keyId, environmentId, name, description, templateId,
-               usage_limit)
+        update(self, orgId, keyId, environmentId, name, description,
+               templateId, usage_limit)

-        add_pool(self, keyId, poolid)
+        add_pool(self, orgId, keyId, poolid)

-        remove_pool(self, keyId, poolid)
+        remove_pool(self, orgId, keyId, poolid)

-        delete(self, keyId)
+        delete(self, orgId, keyId)

         add_system_group(self, org_name, activation_key_id, system_group_id)



```

katello-cli-1.1.6-1
=======================

```diff

diff --git a/katello.client.api.base b/katello.client.api.base
index 7be614a..17707c2 100644
--- a/katello.client.api.base
+++ b/katello.client.api.base
@@ -13,12 +13,6 @@ CLASSES
             x.__init__(...) initializes x; see x.__class__.__doc__ for
             signature

-        Class methods
-
-            update_dict(cls, d, key, value)
-                Update value for key in fictionary only if the value is not
-                None.
-
         Instance variables

             server(self)
diff --git a/katello.client.api.system b/katello.client.api.system
index 2c78189..688e3cf 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -28,7 +28,8 @@ CLASSES

         system(self, system_id)

-        tasks(self, org_name, environment_id, system_name)
+        tasks(self, org_name, environment_id, system_name=None,
+              system_uuid=None)

         packages(self, system_id)

diff --git a/katello.client.api.utils b/katello.client.api.utils
index b6809a3..96b7bc5 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -61,4 +61,4 @@ FUNCTIONS

     get_system_group(org_name, system_group_name)

-    get_system(org_name, sys_name, env_name=None)
+    get_system(org_name, sys_name, env_name=None, sys_uuid=None)


```

katello-cli-1.1.4-1
=======================

```diff

diff --git a/katello.client.api.admin b/katello.client.api.admin
index e652a09..368b919 100644
--- a/katello.client.api.admin
+++ b/katello.client.api.admin
@@ -8,4 +8,4 @@ CLASSES
         Various administrative actions


-        crl_regen(self, query={})
+        crl_regen(self, query=None)
diff --git a/katello.client.api.base b/katello.client.api.base
index fad59e8..7be614a 100644
--- a/katello.client.api.base
+++ b/katello.client.api.base
@@ -9,9 +9,15 @@ CLASSES
         after instantiation.


-        update_dict(self, d, key, value)
-            Update value for key in fictionary only if the value is not
-            None.
+        __init__(self)
+            x.__init__(...) initializes x; see x.__class__.__doc__ for
+            signature
+
+        Class methods
+
+            update_dict(cls, d, key, value)
+                Update value for key in fictionary only if the value is not
+                None.

         Instance variables

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 5d37049..be92fe9 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,7 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name, type, description=None)
+        create(self, orgName, envId, name, type_in, description=None)

         update(self, csId, newName, description)

diff --git a/katello.client.api.errata b/katello.client.api.errata
index 132bb55..335b161 100644
--- a/katello.client.api.errata
+++ b/katello.client.api.errata
@@ -5,11 +5,12 @@ MODULE NAME
 CLASSES

     class ErrataAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access errata calls


         errata_filter(self, repo_id=None, environment_id=None, prod_id=None,
-                      type=None, severity=None)
+                      type_in=None, severity=None)

-        errata_by_repo(self, repoId, type=None)
+        errata_by_repo(self, repoId, type_in=None)

         errata(self, errata_id, repoId)
diff --git a/katello.client.api.permission b/katello.client.api.permission
index 4fbf368..ae89fcb 100644
--- a/katello.client.api.permission
+++ b/katello.client.api.permission
@@ -8,10 +8,10 @@ CLASSES
         Connection class to access Permissions


-        create(self, roleId, name, description, type, verbs, tagIds,
-               orgId=None)
+        create(self, roleId, name, description, type_in, verbs, tagIds,
+               orgId=None, all_tags=False)

-        permissions(self, roleId, query={})
+        permissions(self, roleId, query=None)

         permission(self, roleId, permissionId)

diff --git a/katello.client.api.sync_plan b/katello.client.api.sync_plan
index 5f5b8e6..fad7b6c 100644
--- a/katello.client.api.sync_plan
+++ b/katello.client.api.sync_plan
@@ -13,7 +13,7 @@ CLASSES
         update(self, org_id, plan_id, name, sync_date, interval,
                description)

-        sync_plans(self, org_id, query={})
+        sync_plans(self, org_id, query=None)

         sync_plan(self, org_id, plan_id)

diff --git a/katello.client.api.system b/katello.client.api.system
index ec9dc2d..2c78189 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -9,7 +9,7 @@ CLASSES


         register(self, name, org, environment_id, activation_keys, cp_type,
-                 release=None, sla=None, facts={})
+                 release=None, sla=None, facts=None)

         unregister(self, system_uuid)

@@ -17,7 +17,8 @@ CLASSES

         subscriptions(self, system_id)

-        available_pools(self, system_id)
+        available_pools(self, system_id, match_system=False,
+                        match_installed=False, no_overlap=False)

         unsubscribe(self, system_id, entitlement)

@@ -35,7 +36,7 @@ CLASSES

         releases_for_environment(self, env_id)

-        update(self, system_id, params={})
+        update(self, system_id, params=None)

         install_packages(self, system_id, packages)

@@ -47,15 +48,15 @@ CLASSES

         remove_package_groups(self, system_id, packages)

-        systems_by_org(self, orgId, query={})
+        systems_by_org(self, orgId, query=None)

-        systems_by_env(self, org_name, environment_id, query={})
+        systems_by_env(self, environment_id, query=None)

         errata(self, system_id)

-        report_by_org(self, orgId, format)
+        report_by_org(self, orgId, format_in)

-        report_by_env(self, env_id, format)
+        report_by_env(self, env_id, format_in)

         add_system_groups(self, system_id, system_group_ids)

diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index d105f93..a9dd56d 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -8,15 +8,15 @@ CLASSES
         Connection class to access environment calls


-        system_groups(self, org_id, query={})
+        system_groups(self, org_id, query=None)

-        system_group(self, org_id, system_group_id, query={})
+        system_group(self, org_id, system_group_id, query=None)

         system_group_history(self, org_id, system_group_id, job_id=None)

-        system_group_by_name(self, org_id, system_group_name, query={})
+        system_group_by_name(self, org_id, system_group_name)

-        system_group_systems(self, org_id, system_group_id, query={})
+        system_group_systems(self, org_id, system_group_id, query=None)

         create(self, org_id, name, description, max_systems)

@@ -44,6 +44,6 @@ CLASSES

         remove_package_groups(self, org_id, system_group_id, packages)

-        errata(self, org_id, system_group_id, type=None)
+        errata(self, org_id, system_group_id, type_in=None)

         install_errata(self, org_id, system_group_id, errata)
diff --git a/katello.client.api.template b/katello.client.api.template
index e022517..30d22c0 100644
--- a/katello.client.api.template
+++ b/katello.client.api.template
@@ -15,9 +15,9 @@ CLASSES

         import_tpl(self, envId, description, tplFile)

-        validate_tpl(self, tplId, format)
+        validate_tpl(self, tplId, format_in)

-        export_tpl(self, tplId, format)
+        export_tpl(self, tplId, format_in)

         create(self, envId, name, description, parentId)

diff --git a/katello.client.api.user b/katello.client.api.user
index f7ed01a..1e390b8 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -16,7 +16,7 @@ CLASSES
         update(self, user_id, pw, email, disabled, default_environment,
                default_locale=None)

-        users(self, query={})
+        users(self, query=None)

         user(self, user_id)

@@ -30,4 +30,4 @@ CLASSES

         roles(self, user_id)

-        report(self, format)
+        report(self, format_in)
diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 3751f1f..1eecbca 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -10,7 +10,7 @@ CLASSES

         create(self, name, description)

-        roles(self, query={})
+        roles(self, query=None)

         role(self, role_id)



```

katello-cli-1.1.3-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 96a908a..5d37049 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,7 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name, description=None)
+        create(self, orgName, envId, name, type, description=None)

         update(self, csId, newName, description)

@@ -21,7 +21,7 @@ CLASSES

         dependencies(self, csId)

-        promote(self, csId)
+        apply(self, csId)

         update_content(self, csId, patch)

diff --git a/katello.client.api.user b/katello.client.api.user
index 10a6546..f7ed01a 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,11 +8,13 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, email, disabled, default_environment)
+        create(self, name, pw, email, disabled, default_environment,
+               default_locale=None)

         delete(self, user_id)

-        update(self, user_id, pw, email, disabled, default_environment)
+        update(self, user_id, pw, email, disabled, default_environment,
+               default_locale=None)

         users(self, query={})



```

katello-cli-1.1.1-1
=======================

```diff

diff --git a/katello.client.api.utils b/katello.client.api.utils
index f8cc14a..b6809a3 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -1,12 +1,20 @@
 MODULE NAME
     katello.client.api.utils

+DESCRIPTION
+    Bunch of api utilities for finding records by their names. Katello API
+    uses integer ids for record identification in most cases. These util
+    functions help with translating names to ids. All of them throw
+    ApiDataError if any of the records is not found.
+

 CLASSES

     class ApiDataError(exceptions.Exception)
         Exception to indicate an error in search for data via api. The only
-        argument is [0] the error message.
+        argument is the error message.
+
+        :argument: localized error message

         Inherited methods
             exceptions.BaseException.__delattr__(...)


```

katello-cli-0.2.44-1
=======================

```diff

diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index ad8bfca..d105f93 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -20,6 +20,9 @@ CLASSES

         create(self, org_id, name, description, max_systems)

+        copy(self, org_id, system_group_id, new_name, description,
+             max_systems)
+
         update(self, org_id, system_group_id, name, description,
                max_systems)



```

katello-cli-0.2.43-1
=======================

```diff

diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index d1103e5..ad8bfca 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -18,10 +18,6 @@ CLASSES

         system_group_systems(self, org_id, system_group_id, query={})

-        lock(self, org_id, system_group_id, query={})
-
-        unlock(self, org_id, system_group_id, query={})
-
         create(self, org_id, name, description, max_systems)

         update(self, org_id, system_group_id, name, description,
@@ -45,4 +41,6 @@ CLASSES

         remove_package_groups(self, org_id, system_group_id, packages)

+        errata(self, org_id, system_group_id, type=None)
+
         install_errata(self, org_id, system_group_id, errata)


```

katello-cli-0.2.42-1
=======================

```diff

diff --git a/katello.client.api.job b/katello.client.api.job
new file mode 100644
index 0000000..fb10dc0
--- /dev/null
+++ b/katello.client.api.job
@@ -0,0 +1,12 @@
+MODULE NAME
+    katello.client.api.job
+
+
+CLASSES
+
+    class SystemGroupJobStatusAPI(katello.client.api.base.KatelloAPI)
+
+
+        __init__(self, org_id, system_group_id)
+
+        status(self, jobId)
diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index 7e1aaf1..d1103e5 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -12,7 +12,7 @@ CLASSES

         system_group(self, org_id, system_group_id, query={})

-        system_group_history(self, org_id, system_group_id, query={})
+        system_group_history(self, org_id, system_group_id, job_id=None)

         system_group_by_name(self, org_id, system_group_name, query={})

@@ -32,3 +32,17 @@ CLASSES
         add_systems(self, org_id, system_group_id, system_ids)

         remove_systems(self, org_id, system_group_id, system_ids)
+
+        install_packages(self, org_id, system_group_id, packages)
+
+        update_packages(self, org_id, system_group_id, packages)
+
+        remove_packages(self, org_id, system_group_id, packages)
+
+        install_package_groups(self, org_id, system_group_id, packages)
+
+        update_package_groups(self, org_id, system_group_id, packages)
+
+        remove_package_groups(self, org_id, system_group_id, packages)
+
+        install_errata(self, org_id, system_group_id, errata)


```

katello-cli-0.2.41-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index 87092fb..305ecc5 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -13,9 +13,11 @@ CLASSES

         activation_key(self, keyId)

-        create(self, envId, name, description, templateId=None)
+        create(self, envId, name, description, usage_limit=-1,
+               templateId=None)

-        update(self, keyId, environmentId, name, description, templateId)
+        update(self, keyId, environmentId, name, description, templateId,
+               usage_limit)

         add_pool(self, keyId, poolid)

diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index ef5d0bf..7e1aaf1 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -27,7 +27,7 @@ CLASSES
         update(self, org_id, system_group_id, name, description,
                max_systems)

-        delete(self, org_id, system_group_id)
+        delete(self, org_id, system_group_id, delete_systems)

         add_systems(self, org_id, system_group_id, system_ids)



```

katello-cli-0.2.39-1
=======================

```diff

diff --git a/katello.client.api.admin b/katello.client.api.admin
index 368b919..e652a09 100644
--- a/katello.client.api.admin
+++ b/katello.client.api.admin
@@ -8,4 +8,4 @@ CLASSES
         Various administrative actions


-        crl_regen(self, query=None)
+        crl_regen(self, query={})
diff --git a/katello.client.api.system_group b/katello.client.api.system_group
index 16305c1..ef5d0bf 100644
--- a/katello.client.api.system_group
+++ b/katello.client.api.system_group
@@ -12,6 +12,8 @@ CLASSES

         system_group(self, org_id, system_group_id, query={})

+        system_group_history(self, org_id, system_group_id, query={})
+
         system_group_by_name(self, org_id, system_group_name, query={})

         system_group_systems(self, org_id, system_group_id, query={})


```

katello-cli-0.2.38-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index b5b5f7d..87092fb 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -22,3 +22,8 @@ CLASSES
         remove_pool(self, keyId, poolid)

         delete(self, keyId)
+
+        add_system_group(self, org_name, activation_key_id, system_group_id)
+
+        remove_system_group(self, org_name, activation_key_id,
+                            system_group_id)
diff --git a/katello.client.api.system b/katello.client.api.system
index 13d705f..ec9dc2d 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -57,4 +57,8 @@ CLASSES

         report_by_env(self, env_id, format)

+        add_system_groups(self, system_id, system_group_ids)
+
+        remove_system_groups(self, system_id, system_group_ids)
+
         remove_consumer_deletion_record(self, uuid)
diff --git a/katello.client.api.system_group b/katello.client.api.system_group
new file mode 100644
index 0000000..16305c1
--- /dev/null
+++ b/katello.client.api.system_group
@@ -0,0 +1,32 @@
+MODULE NAME
+    katello.client.api.system_group
+
+
+CLASSES
+
+    class SystemGroupAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access environment calls
+
+
+        system_groups(self, org_id, query={})
+
+        system_group(self, org_id, system_group_id, query={})
+
+        system_group_by_name(self, org_id, system_group_name, query={})
+
+        system_group_systems(self, org_id, system_group_id, query={})
+
+        lock(self, org_id, system_group_id, query={})
+
+        unlock(self, org_id, system_group_id, query={})
+
+        create(self, org_id, name, description, max_systems)
+
+        update(self, org_id, system_group_id, name, description,
+               max_systems)
+
+        delete(self, org_id, system_group_id)
+
+        add_systems(self, org_id, system_group_id, system_ids)
+
+        remove_systems(self, org_id, system_group_id, system_ids)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 7a0fb62..f8cc14a 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -51,4 +51,6 @@ FUNCTIONS

     get_filter(org_name, name)

+    get_system_group(org_name, system_group_name)
+
     get_system(org_name, sys_name, env_name=None)


```

katello-cli-0.2.34-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index 07a6679..13d705f 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -56,3 +56,5 @@ CLASSES
         report_by_org(self, orgId, format)

         report_by_env(self, env_id, format)
+
+        remove_consumer_deletion_record(self, uuid)


```

katello-cli-0.2.33-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index b1a5fbb..07a6679 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access environment calls


-        register(self, name, org, envName, activation_keys, cp_type,
+        register(self, name, org, environment_id, activation_keys, cp_type,
                  release=None, sla=None, facts={})

         unregister(self, system_uuid)
@@ -27,7 +27,7 @@ CLASSES

         system(self, system_id)

-        tasks(self, org_name, env_name, system_name)
+        tasks(self, org_name, environment_id, system_name)

         packages(self, system_id)

@@ -49,7 +49,7 @@ CLASSES

         systems_by_org(self, orgId, query={})

-        systems_by_env(self, orgId, envName, query={})
+        systems_by_env(self, org_name, environment_id, query={})

         errata(self, system_id)

diff --git a/katello.client.api.utils b/katello.client.api.utils
index 1824ece..7a0fb62 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -2,6 +2,26 @@ MODULE NAME
     katello.client.api.utils


+CLASSES
+
+    class ApiDataError(exceptions.Exception)
+        Exception to indicate an error in search for data via api. The only
+        argument is [0] the error message.
+
+        Inherited methods
+            exceptions.BaseException.__delattr__(...)
+            exceptions.BaseException.__getattribute__(...)
+            exceptions.BaseException.__getitem__(x, y)
+            exceptions.BaseException.__getslice__(x, i, j)
+            exceptions.Exception.__init__(...)
+            exceptions.Exception.__new__(T, S, *...)
+            exceptions.BaseException.__reduce__(...)
+            exceptions.BaseException.__repr__(x)
+            exceptions.BaseException.__setattr__(...)
+            exceptions.BaseException.__setstate__(...)
+            exceptions.BaseException.__str__(x)
+            exceptions.BaseException.__unicode__(...)
+
 FUNCTIONS

     get_organization(orgName)
@@ -30,3 +50,5 @@ FUNCTIONS
     get_permission(role_name, permission_name)

     get_filter(org_name, name)
+
+    get_system(org_name, sys_name, env_name=None)
diff --git a/katello.client.api.version b/katello.client.api.version
new file mode 100644
index 0000000..6c41a95
--- /dev/null
+++ b/katello.client.api.version
@@ -0,0 +1,13 @@
+MODULE NAME
+    katello.client.api.version
+
+
+CLASSES
+
+    class VersionAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access  version information
+
+
+        version(self)
+
+        version_formatted(self)


```

katello-cli-0.2.32-1
=======================

```diff

diff --git a/katello.client.api.package b/katello.client.api.package
index f41475f..a45d238 100644
--- a/katello.client.api.package
+++ b/katello.client.api.package
@@ -11,3 +11,5 @@ CLASSES
         package(self, packageId, repoId)

         packages_by_repo(self, repoId)
+
+        search(self, query, repoId)
diff --git a/katello.client.api.user b/katello.client.api.user
index aa7ea4d..10a6546 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -20,6 +20,8 @@ CLASSES

         user_by_name(self, user_name)

+        sync_ldap_roles(self)
+
         assign_role(self, user_id, role_id)

         unassign_role(self, user_id, role_id)
diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 3476461..3751f1f 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -21,3 +21,9 @@ CLASSES
         update(self, role_id, name, desc)

         available_verbs(self, orgName)
+
+        add_ldap_group(self, role_id, group_name)
+
+        remove_ldap_group(self, role_id, group_name)
+
+        ldap_groups(self, role_id)


```

katello-cli-0.2.31-1
=======================

```diff

diff --git a/katello.client.api.user b/katello.client.api.user
index 882dbe3..aa7ea4d 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,11 +8,11 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, email, disabled)
+        create(self, name, pw, email, disabled, default_environment)

         delete(self, user_id)

-        update(self, user_id, pw, email, disabled)
+        update(self, user_id, pw, email, disabled, default_environment)

         users(self, query={})



```

katello-cli-0.2.28-1
=======================

```diff

diff --git a/katello.client.api.organization b/katello.client.api.organization
index da1a42c..c0902b9 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -18,6 +18,6 @@ CLASSES

         organization(self, name)

-        uebercert(self, name)
+        uebercert(self, name, regenerate=False)

         pools(self, name)


```

katello-cli-0.2.25-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index b8f3d13..b1a5fbb 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -9,7 +9,7 @@ CLASSES


         register(self, name, org, envName, activation_keys, cp_type,
-                 release=None, sla=None)
+                 release=None, sla=None, facts={})

         unregister(self, system_uuid)



```

katello-cli-0.2.19-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index 12dea52..b8f3d13 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -9,7 +9,7 @@ CLASSES


         register(self, name, org, envName, activation_keys, cp_type,
-                 release=None)
+                 release=None, sla=None)

         unregister(self, system_uuid)



```

katello-cli-0.2.18-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index 89a515f..12dea52 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -8,7 +8,8 @@ CLASSES
         Connection class to access environment calls


-        register(self, name, org, envName, activation_keys, cp_type)
+        register(self, name, org, envName, activation_keys, cp_type,
+                 release=None)

         unregister(self, system_uuid)

@@ -30,6 +31,10 @@ CLASSES

         packages(self, system_id)

+        releases_for_system(self, system_id)
+
+        releases_for_environment(self, env_id)
+
         update(self, system_id, params={})

         install_packages(self, system_id, packages)


```

katello-cli-0.2.16-1
=======================

```diff

diff --git a/katello.client.api.provider b/katello.client.api.provider
index ff2a1d0..33c01b8 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -27,3 +27,5 @@ CLASSES
         last_sync_status(self, provId)

         import_manifest(self, provId, manifestFile, force=False)
+
+        refresh_products(self, provId)


```

katello-cli-0.2.15-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 1853fc2..96a908a 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -15,6 +15,8 @@ CLASSES

         create(self, orgName, envId, name, description=None)

+        update(self, csId, newName, description)
+
         delete(self, csId)

         dependencies(self, csId)


```

katello-cli-0.2.11-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index d846476..89a515f 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -50,4 +50,4 @@ CLASSES

         report_by_org(self, orgId, format)

-        report_by_env(self, orgId, envName, format)
+        report_by_env(self, env_id, format)


```

katello-cli-0.2.9-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 352c253..1853fc2 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,7 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name)
+        create(self, orgName, envId, name, description=None)

         delete(self, csId)



```

katello-cli-0.2.1-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 96a908a..352c253 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,9 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name, description=None)
-
-        update(self, csId, newName, description)
+        create(self, orgName, envId, name)

         delete(self, csId)

diff --git a/katello.client.api.system b/katello.client.api.system
index 89a515f..d846476 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -50,4 +50,4 @@ CLASSES

         report_by_org(self, orgId, format)

-        report_by_env(self, env_id, format)
+        report_by_env(self, orgId, envName, format)


```

katello-cli-0.1.106-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 1853fc2..96a908a 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -15,6 +15,8 @@ CLASSES

         create(self, orgName, envId, name, description=None)

+        update(self, csId, newName, description)
+
         delete(self, csId)

         dependencies(self, csId)


```

katello-cli-0.1.104-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 352c253..1853fc2 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -13,7 +13,7 @@ CLASSES

         changeset_by_name(self, orgName, envId, csName)

-        create(self, orgName, envId, name)
+        create(self, orgName, envId, name, description=None)

         delete(self, csId)

diff --git a/katello.client.api.system b/katello.client.api.system
index d846476..89a515f 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -50,4 +50,4 @@ CLASSES

         report_by_org(self, orgId, format)

-        report_by_env(self, orgId, envName, format)
+        report_by_env(self, env_id, format)


```

katello-cli-0.1.53-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index a9f81be..12883de 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -18,23 +18,23 @@ CLASSES

         create(self, provId, name, description, gpgkey)

-        update(self, prodId, description, gpgkey, nogpgkey,
+        update(self, orgName, prodId, description, gpgkey, nogpgkey,
                gpgkey_recursive)

-        show(self, prodId)
+        show(self, orgName, prodId)

-        delete(self, prodId)
+        delete(self, orgName, prodId)

-        sync(self, prodId)
+        sync(self, orgName, prodId)

-        set_sync_plan(self, prodId, planId)
+        set_sync_plan(self, orgName, prodId, planId)

-        remove_sync_plan(self, prodId)
+        remove_sync_plan(self, orgName, prodId)

-        cancel_sync(self, prodId)
+        cancel_sync(self, orgName, prodId)

-        last_sync_status(self, prodId)
+        last_sync_status(self, orgName, prodId)

-        update_filters(self, prodId, filters)
+        update_filters(self, orgName, prodId, filters)

-        filters(self, prodId)
+        filters(self, orgName, prodId)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index c9a4885..5d9d023 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,7 +8,7 @@ CLASSES
         Connection class to access repositories


-        create(self, prod_id, name, url, gpgkey, nogpgkey)
+        create(self, orgName, prod_id, name, url, gpgkey, nogpgkey)

         update(self, repo_id, gpgkey, nogpgkey)

@@ -17,7 +17,7 @@ CLASSES
         repos_by_env_product(self, envId, productId, name=None,
                              includeDisabled=False)

-        repos_by_product(self, productId, includeDisabled=False)
+        repos_by_product(self, orgName, productId, includeDisabled=False)

         repo(self, repo_id)



```

katello-cli-0.1.50-1
=======================

```diff

diff --git a/katello.client.api.repo b/katello.client.api.repo
index 9229493..c9a4885 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -45,4 +45,4 @@ CLASSES

         update_filters(self, repo_id, filters)

-        filters(self, repo_id)
+        filters(self, repo_id, inherit=False)


```

katello-cli-0.1.45-1
=======================

```diff

diff --git a/katello.client.api.repo b/katello.client.api.repo
index dba79c6..9229493 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -42,3 +42,7 @@ CLASSES
         packagegroupcategories(self, repoid)

         packagegroupcategory_by_id(self, repoid, categoryId)
+
+        update_filters(self, repo_id, filters)
+
+        filters(self, repo_id)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index a2f6d53..1824ece 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -28,3 +28,5 @@ FUNCTIONS
     get_sync_plan(org_name, name)

     get_permission(role_name, permission_name)
+
+    get_filter(org_name, name)


```

katello-cli-0.1.44-1
=======================

```diff

diff --git a/katello.client.api.environment b/katello.client.api.environment
index cc99ace..6e3b547 100644
--- a/katello.client.api.environment
+++ b/katello.client.api.environment
@@ -14,7 +14,7 @@ CLASSES

         environment_by_name(self, orgId, envName)

-        locker_by_org(self, orgId)
+        library_by_org(self, orgId)

         create(self, orgId, name, description, priorId)

diff --git a/katello.client.api.utils b/katello.client.api.utils
index e1c0dd7..a2f6d53 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -8,7 +8,7 @@ FUNCTIONS

     get_environment(orgName, envName=None)

-    get_locker(orgName)
+    get_library(orgName)

     get_product(orgName, prodName)



```

katello-cli-0.1.41-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index ef4de05..d846476 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -18,7 +18,11 @@ CLASSES

         available_pools(self, system_id)

-        unsubscribe(self, system_id, pool)
+        unsubscribe(self, system_id, entitlement)
+
+        unsubscribe_by_serial(self, system_id, serial)
+
+        unsubscribe_all(self, system_id)

         system(self, system_id)



```

katello-cli-0.1.39-1
=======================

```diff

diff --git a/katello.client.api.errata b/katello.client.api.errata
index 6798d51..132bb55 100644
--- a/katello.client.api.errata
+++ b/katello.client.api.errata
@@ -12,4 +12,4 @@ CLASSES

         errata_by_repo(self, repoId, type=None)

-        errata(self, errata_id)
+        errata(self, errata_id, repoId)
diff --git a/katello.client.api.package b/katello.client.api.package
index 99e9978..f41475f 100644
--- a/katello.client.api.package
+++ b/katello.client.api.package
@@ -8,6 +8,6 @@ CLASSES
         Connection class to access package calls


-        package(self, packageId)
+        package(self, packageId, repoId)

         packages_by_repo(self, repoId)


```

katello-cli-0.1.37-1
=======================

```diff

diff --git a/katello.client.api.gpg_key b/katello.client.api.gpg_key
new file mode 100644
index 0000000..1547d65
--- /dev/null
+++ b/katello.client.api.gpg_key
@@ -0,0 +1,18 @@
+MODULE NAME
+    katello.client.api.gpg_key
+
+
+CLASSES
+
+    class GpgKeyAPI(katello.client.api.base.KatelloAPI)
+
+
+        gpg_keys_by_organization(self, orgId, keyName=None)
+
+        gpg_key(self, keyId)
+
+        create(self, orgName, name, content)
+
+        update(self, keyId, name, content)
+
+        delete(self, keyId)
diff --git a/katello.client.api.product b/katello.client.api.product
index 81a73e1..a9f81be 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -16,7 +16,10 @@ CLASSES

         product_by_name(self, orgName, prodName)

-        create(self, provId, name, description)
+        create(self, provId, name, description, gpgkey)
+
+        update(self, prodId, description, gpgkey, nogpgkey,
+               gpgkey_recursive)

         show(self, prodId)

diff --git a/katello.client.api.repo b/katello.client.api.repo
index 116a3a8..dba79c6 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -8,7 +8,9 @@ CLASSES
         Connection class to access repositories


-        create(self, prod_id, name, url)
+        create(self, prod_id, name, url, gpgkey, nogpgkey)
+
+        update(self, repo_id, gpgkey, nogpgkey)

         repos_by_org_env(self, orgName, envId, includeDisabled=False)



```

katello-cli-0.1.36-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index c668ac9..81a73e1 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -24,6 +24,10 @@ CLASSES

         sync(self, prodId)

+        set_sync_plan(self, prodId, planId)
+
+        remove_sync_plan(self, prodId)
+
         cancel_sync(self, prodId)

         last_sync_status(self, prodId)


```

katello-cli-0.1.35-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 772b3f2..352c253 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -17,6 +17,8 @@ CLASSES

         delete(self, csId)

+        dependencies(self, csId)
+
         promote(self, csId)

         update_content(self, csId, patch)


```

katello-cli-0.1.33-1
=======================

```diff

diff --git a/katello.client.api.sync_plan b/katello.client.api.sync_plan
new file mode 100644
index 0000000..5f5b8e6
--- /dev/null
+++ b/katello.client.api.sync_plan
@@ -0,0 +1,22 @@
+MODULE NAME
+    katello.client.api.sync_plan
+
+
+CLASSES
+
+    class SyncPlanAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access SyncPlans
+
+
+        create(self, org_id, name, sync_date, interval, description)
+
+        update(self, org_id, plan_id, name, sync_date, interval,
+               description)
+
+        sync_plans(self, org_id, query={})
+
+        sync_plan(self, org_id, plan_id)
+
+        sync_plan_by_name(self, org_id, name)
+
+        delete(self, org_id, plan_id)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index f489c38..e1c0dd7 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -25,4 +25,6 @@ FUNCTIONS

     get_role(name)

+    get_sync_plan(org_name, name)
+
     get_permission(role_name, permission_name)


```

katello-cli-0.1.32-1
=======================

```diff

diff --git a/katello.client.api.distribution b/katello.client.api.distribution
index b83fde6..d39910d 100644
--- a/katello.client.api.distribution
+++ b/katello.client.api.distribution
@@ -10,4 +10,4 @@ CLASSES

         distributions_by_repo(self, repoId)

-        distribution(self, distribution_id)
+        distribution(self, repoId, distribution_id)
diff --git a/katello.client.api.permission b/katello.client.api.permission
new file mode 100644
index 0000000..4fbf368
--- /dev/null
+++ b/katello.client.api.permission
@@ -0,0 +1,20 @@
+MODULE NAME
+    katello.client.api.permission
+
+
+CLASSES
+
+    class PermissionAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access Permissions
+
+
+        create(self, roleId, name, description, type, verbs, tagIds,
+               orgId=None)
+
+        permissions(self, roleId, query={})
+
+        permission(self, roleId, permissionId)
+
+        permission_by_name(self, roleId, name)
+
+        delete(self, roleId, permissionId)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 271859d..f489c38 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -22,3 +22,7 @@ FUNCTIONS
     get_changeset(orgName, envName, csName)

     get_user(userName)
+
+    get_role(name)
+
+    get_permission(role_name, permission_name)


```

katello-cli-0.1.31-1
=======================

```diff

diff --git a/katello.client.api.system b/katello.client.api.system
index 47100d8..ef4de05 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -22,10 +22,22 @@ CLASSES

         system(self, system_id)

+        tasks(self, org_name, env_name, system_name)
+
         packages(self, system_id)

         update(self, system_id, params={})

+        install_packages(self, system_id, packages)
+
+        remove_packages(self, system_id, packages)
+
+        update_packages(self, system_id, packages)
+
+        install_package_groups(self, system_id, packages)
+
+        remove_package_groups(self, system_id, packages)
+
         systems_by_org(self, orgId, query={})

         systems_by_env(self, orgId, envName, query={})
diff --git a/katello.client.api.task_status b/katello.client.api.task_status
index 65c5bab..89273c1 100644
--- a/katello.client.api.task_status
+++ b/katello.client.api.task_status
@@ -8,3 +8,8 @@ CLASSES


         status(self, taskUuid)
+
+    class SystemTaskStatusAPI(katello.client.api.base.KatelloAPI)
+
+
+        status(self, taskUuid)


```

katello-cli-0.1.30-1
=======================

```diff

diff --git a/katello.client.api.user b/katello.client.api.user
index 0f6d753..882dbe3 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -18,4 +18,12 @@ CLASSES

         user(self, user_id)

+        user_by_name(self, user_name)
+
+        assign_role(self, user_id, role_id)
+
+        unassign_role(self, user_id, role_id)
+
+        roles(self, user_id)
+
         report(self, format)
diff --git a/katello.client.api.user_role b/katello.client.api.user_role
index 4e0d0c7..3476461 100644
--- a/katello.client.api.user_role
+++ b/katello.client.api.user_role
@@ -19,3 +19,5 @@ CLASSES
         delete(self, role_id)

         update(self, role_id, name, desc)
+
+        available_verbs(self, orgName)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index e85014a..271859d 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -20,3 +20,5 @@ FUNCTIONS
     get_template(orgName, envName, tplName)

     get_changeset(orgName, envName, csName)
+
+    get_user(userName)


```

katello-cli-0.1.26-1
=======================

```diff

diff --git a/katello.client.api.utils b/katello.client.api.utils
index f405cb8..e85014a 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -12,7 +12,8 @@ FUNCTIONS

     get_product(orgName, prodName)

-    get_repo(orgName, prodName, repoName, envName=None)
+    get_repo(orgName, prodName, repoName, envName=None,
+             includeDisabled=False)

     get_provider(orgName, provName)



```

katello-cli-0.1.23-2
=======================

```diff

diff --git a/katello.client.api.provider b/katello.client.api.provider
index aa1e4c1..ff2a1d0 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -26,4 +26,4 @@ CLASSES

         last_sync_status(self, provId)

-        import_manifest(self, provId, manifestFile)
+        import_manifest(self, provId, manifestFile, force=False)


```

katello-cli-0.1.23-1
=======================

```diff

diff --git a/katello.client.api.user_role b/katello.client.api.user_role
new file mode 100644
index 0000000..4e0d0c7
--- /dev/null
+++ b/katello.client.api.user_role
@@ -0,0 +1,21 @@
+MODULE NAME
+    katello.client.api.user_role
+
+
+CLASSES
+
+    class UserRoleAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access User Data
+
+
+        create(self, name, description)
+
+        roles(self, query={})
+
+        role(self, role_id)
+
+        role_by_name(self, name)
+
+        delete(self, role_id)
+
+        update(self, role_id, name, desc)


```

katello-cli-0.1.21-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index b1eb88f..772b3f2 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -20,3 +20,7 @@ CLASSES
         promote(self, csId)

         update_content(self, csId, patch)
+
+        add_content(self, csId, contentType, attrs)
+
+        remove_content(self, csId, contentType, attrs)


```

katello-cli-0.1.20-1
=======================

```diff

diff --git a/katello.client.api.organization b/katello.client.api.organization
index ca37547..da1a42c 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -18,8 +18,6 @@ CLASSES

         organization(self, name)

-        generate_uebercert(self, name)
-
         uebercert(self, name)

         pools(self, name)


```

katello-cli-0.1.17-1
=======================

```diff

diff --git a/katello.client.api.provider b/katello.client.api.provider
index beabb27..aa1e4c1 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -5,7 +5,7 @@ MODULE NAME
 CLASSES

     class ProviderAPI(katello.client.api.base.KatelloAPI)
-        Connection class to access repo specific calls
+        Connection class to access provider specific calls


         create(self, name, orgName, description=None, pType=None, url=None)
@@ -22,6 +22,8 @@ CLASSES

         sync(self, provId)

+        cancel_sync(self, provId)
+
         last_sync_status(self, provId)

         import_manifest(self, provId, manifestFile)


```

katello-cli-0.1.16-1
=======================

```diff

diff --git a/katello.client.api.template b/katello.client.api.template
index 0355c7c..e022517 100644
--- a/katello.client.api.template
+++ b/katello.client.api.template
@@ -15,6 +15,8 @@ CLASSES

         import_tpl(self, envId, description, tplFile)

+        validate_tpl(self, tplId, format)
+
         export_tpl(self, tplId, format)

         create(self, envId, name, description, parentId)
@@ -28,3 +30,7 @@ CLASSES
         promotion_status(self, task_id)

         delete(self, template_id)
+
+        Class variables
+
+            format_content_type = {'json': 'application/json', 'tdl': 'a...


```

katello-cli-0.1.14-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index f8a71c9..c668ac9 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -24,6 +24,8 @@ CLASSES

         sync(self, prodId)

+        cancel_sync(self, prodId)
+
         last_sync_status(self, prodId)

         update_filters(self, prodId, filters)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index b904e7b..116a3a8 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -10,18 +10,23 @@ CLASSES

         create(self, prod_id, name, url)

-        repos_by_org_env(self, orgName, envId)
+        repos_by_org_env(self, orgName, envId, includeDisabled=False)

-        repos_by_env_product(self, envId, productId, name=None)
+        repos_by_env_product(self, envId, productId, name=None,
+                             includeDisabled=False)

-        repos_by_product(self, productId)
+        repos_by_product(self, productId, includeDisabled=False)

         repo(self, repo_id)

+        enable(self, repo_id, enable=True)
+
         delete(self, repoId)

         sync(self, repo_id)

+        cancel_sync(self, repo_id)
+
         last_sync_status(self, repo_id)

         repo_discovery(self, org_name, url, repotype)


```

katello-cli-0.1.12-1
=======================

```diff

diff --git a/katello.client.api.user b/katello.client.api.user
index 689419b..0f6d753 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -8,11 +8,11 @@ CLASSES
         Connection class to access User Data


-        create(self, name, pw, disabled)
+        create(self, name, pw, email, disabled)

         delete(self, user_id)

-        update(self, user_id, pw, disabled)
+        update(self, user_id, pw, email, disabled)

         users(self, query={})



```

katello-cli-0.1.11-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index 0cd66ee..b5b5f7d 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -17,4 +17,8 @@ CLASSES

         update(self, keyId, environmentId, name, description, templateId)

+        add_pool(self, keyId, poolid)
+
+        remove_pool(self, keyId, poolid)
+
         delete(self, keyId)
diff --git a/katello.client.api.errata b/katello.client.api.errata
index a580160..6798d51 100644
--- a/katello.client.api.errata
+++ b/katello.client.api.errata
@@ -5,9 +5,11 @@ MODULE NAME
 CLASSES

     class ErrataAPI(katello.client.api.base.KatelloAPI)
-        Connection class to access errata calls


-        errata_by_repo(self, repoId)
+        errata_filter(self, repo_id=None, environment_id=None, prod_id=None,
+                      type=None, severity=None)
+
+        errata_by_repo(self, repoId, type=None)

         errata(self, errata_id)
diff --git a/katello.client.api.organization b/katello.client.api.organization
index bd5c9db..ca37547 100644
--- a/katello.client.api.organization
+++ b/katello.client.api.organization
@@ -17,3 +17,9 @@ CLASSES
         organizations(self)

         organization(self, name)
+
+        generate_uebercert(self, name)
+
+        uebercert(self, name)
+
+        pools(self, name)
diff --git a/katello.client.api.product b/katello.client.api.product
index 67926d5..f8a71c9 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -18,6 +18,14 @@ CLASSES

         create(self, provId, name, description)

+        show(self, prodId)
+
+        delete(self, prodId)
+
         sync(self, prodId)

         last_sync_status(self, prodId)
+
+        update_filters(self, prodId, filters)
+
+        filters(self, prodId)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index a7a05e4..b904e7b 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -12,12 +12,14 @@ CLASSES

         repos_by_org_env(self, orgName, envId)

-        repos_by_env_product(self, envId, productId)
+        repos_by_env_product(self, envId, productId, name=None)

         repos_by_product(self, productId)

         repo(self, repo_id)

+        delete(self, repoId)
+
         sync(self, repo_id)

         last_sync_status(self, repo_id)
@@ -28,4 +30,8 @@ CLASSES

         packagegroups(self, repoid)

+        packagegroup_by_id(self, repoid, groupId)
+
         packagegroupcategories(self, repoid)
+
+        packagegroupcategory_by_id(self, repoid, categoryId)
diff --git a/katello.client.api.system b/katello.client.api.system
index eb9eff7..47100d8 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -10,7 +10,15 @@ CLASSES

         register(self, name, org, envName, activation_keys, cp_type)

-        unregister(self, system_id)
+        unregister(self, system_uuid)
+
+        subscribe(self, system_id, pool, quantity)
+
+        subscriptions(self, system_id)
+
+        available_pools(self, system_id)
+
+        unsubscribe(self, system_id, pool)

         system(self, system_id)

@@ -23,3 +31,7 @@ CLASSES
         systems_by_env(self, orgId, envName, query={})

         errata(self, system_id)
+
+        report_by_org(self, orgId, format)
+
+        report_by_env(self, orgId, envName, format)
diff --git a/katello.client.api.template b/katello.client.api.template
index f861bee..0355c7c 100644
--- a/katello.client.api.template
+++ b/katello.client.api.template
@@ -15,13 +15,15 @@ CLASSES

         import_tpl(self, envId, description, tplFile)

+        export_tpl(self, tplId, format)
+
         create(self, envId, name, description, parentId)

         update(self, tplId, newName, description, parentId)

-        update_content(self, tplId, actionName, params)
+        add_content(self, tplId, contentType, attrs)

-        promote(self, template_id)
+        remove_content(self, tplId, contentType, contentId)

         promotion_status(self, task_id)

diff --git a/katello.client.api.user b/katello.client.api.user
index 3ba3dbe..689419b 100644
--- a/katello.client.api.user
+++ b/katello.client.api.user
@@ -17,3 +17,5 @@ CLASSES
         users(self, query={})

         user(self, user_id)
+
+        report(self, format)


```

katello-cli-0.1.10-1
=======================

```diff

diff --git a/katello.client.api.utils b/katello.client.api.utils
index 08b87be..f405cb8 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -8,6 +8,8 @@ FUNCTIONS

     get_environment(orgName, envName=None)

+    get_locker(orgName)
+
     get_product(orgName, prodName)

     get_repo(orgName, prodName, repoName, envName=None)


```

katello-cli-0.1.8-1
=======================

```diff

diff --git a/katello.client.api.provider b/katello.client.api.provider
index 7897635..beabb27 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -14,12 +14,14 @@ CLASSES

         update(self, provId, name, description=None, url=None)

-        providers(self)
-
         providers_by_org(self, orgId)

+        provider(self, provId)
+
         provider_by_name(self, orgName, provName)

         sync(self, provId)

+        last_sync_status(self, provId)
+
         import_manifest(self, provId, manifestFile)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index ae75884..a7a05e4 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -22,6 +22,10 @@ CLASSES

         last_sync_status(self, repo_id)

-        repo_discovery(self, url, repotype)
+        repo_discovery(self, org_name, url, repotype)

         repo_discovery_status(self, discoveryTaskId)
+
+        packagegroups(self, repoid)
+
+        packagegroupcategories(self, repoid)


```

katello-cli-0.1.7-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index 6886063..67926d5 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -14,8 +14,6 @@ CLASSES

         products_by_provider(self, provId, prodName=None)

-        products(self)
-
         product_by_name(self, orgName, prodName)

         create(self, provId, name, description)
diff --git a/katello.client.api.provider b/katello.client.api.provider
index 48ec192..7897635 100644
--- a/katello.client.api.provider
+++ b/katello.client.api.provider
@@ -18,8 +18,6 @@ CLASSES

         providers_by_org(self, orgId)

-        provider(self, provId)
-
         provider_by_name(self, orgName, provName)

         sync(self, provId)


```

katello-cli-0.1.6-1
=======================

```diff

diff --git a/katello.client.api.product b/katello.client.api.product
index 34d2dc2..6886063 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -21,3 +21,5 @@ CLASSES
         create(self, provId, name, description)

         sync(self, prodId)
+
+        last_sync_status(self, prodId)
diff --git a/katello.client.api.repo b/katello.client.api.repo
index 7f7682d..ae75884 100644
--- a/katello.client.api.repo
+++ b/katello.client.api.repo
@@ -20,6 +20,8 @@ CLASSES

         sync(self, repo_id)

+        last_sync_status(self, repo_id)
+
         repo_discovery(self, url, repotype)

         repo_discovery_status(self, discoveryTaskId)


```

katello-cli-0.1.5-1
=======================

```diff

diff --git a/katello.client.api.changeset b/katello.client.api.changeset
index 1616aa3..b1eb88f 100644
--- a/katello.client.api.changeset
+++ b/katello.client.api.changeset
@@ -15,8 +15,8 @@ CLASSES

         create(self, orgName, envId, name)

-        delete(self, orgName, envId, csId)
+        delete(self, csId)

-        promote(self, orgName, envId, csId)
+        promote(self, csId)

-        update_content(self, orgName, envId, csId, patch)
+        update_content(self, csId, patch)
diff --git a/katello.client.api.product b/katello.client.api.product
index 3271517..34d2dc2 100644
--- a/katello.client.api.product
+++ b/katello.client.api.product
@@ -10,7 +10,7 @@ CLASSES

         products_by_org(self, orgName, prodName=None)

-        products_by_env(self, orgName, envName)
+        products_by_env(self, envId)

         products_by_provider(self, provId, prodName=None)

@@ -18,6 +18,6 @@ CLASSES

         product_by_name(self, orgName, prodName)

-        create(self, provId, name, description, url)
+        create(self, provId, name, description)

         sync(self, prodId)
diff --git a/katello.client.api.system b/katello.client.api.system
index 88f9c95..eb9eff7 100644
--- a/katello.client.api.system
+++ b/katello.client.api.system
@@ -8,12 +8,18 @@ CLASSES
         Connection class to access environment calls


-        register(self, name, org, envName, cp_type)
+        register(self, name, org, envName, activation_keys, cp_type)

         unregister(self, system_id)

         system(self, system_id)

+        packages(self, system_id)
+
+        update(self, system_id, params={})
+
         systems_by_org(self, orgId, query={})

         systems_by_env(self, orgId, envName, query={})
+
+        errata(self, system_id)


```

katello-cli-0.1.4-1
=======================

```diff

diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
index b5b5f7d..0cd66ee 100644
--- a/katello.client.api.activation_key
+++ b/katello.client.api.activation_key
@@ -17,8 +17,4 @@ CLASSES

         update(self, keyId, environmentId, name, description, templateId)

-        add_pool(self, keyId, poolid)
-
-        remove_pool(self, keyId, poolid)
-
         delete(self, keyId)
diff --git a/katello.client.api.utils b/katello.client.api.utils
index 4df176b..08b87be 100644
--- a/katello.client.api.utils
+++ b/katello.client.api.utils
@@ -4,6 +4,8 @@ MODULE NAME

 FUNCTIONS

+    get_organization(orgName)
+
     get_environment(orgName, envName=None)

     get_product(orgName, prodName)


```

katello-cli-0.1.1-1
=======================

```diff

diff --git a/cli_test_utils b/cli_test_utils
new file mode 100644
index 0000000..e373ae1
--- /dev/null
+++ b/cli_test_utils
@@ -0,0 +1,194 @@
+MODULE NAME
+    cli_test_utils
+
+
+CLASSES
+
+    class CLITestCase(unittest.TestCase)
+
+
+        mock_from_module(self, property_name, return_value=None)
+
+        mock(self, obj, property_name, return_value=None)
+
+        restore_mocks(self)
+
+        set_action(self, action)
+
+        set_module(self, module)
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        Class variables
+
+            _mocked_props = {}
+
+            action = None
+
+            module = None
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.setUp(self)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class CLIOptionTestCase(CLITestCase)
+
+
+        mock_options(self)
+
+        mocked_get_option(self, opt, default=None)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.setUp(self)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class CLIActionTestCase(CLITestCase)
+
+
+        mock_printer(self)
+
+        mock_options(self, options)
+
+        mocked_get_option(self, opt, default=None)
+
+        mock_spinner(self)
+
+        Class variables
+
+            _options = {}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.setUp(self)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/conf b/conf
new file mode 100644
index 0000000..199232e
--- /dev/null
+++ b/conf
@@ -0,0 +1,35 @@
+MODULE NAME
+    conf
+
+
+VARIABLES
+
+    extensions = ['sphinx.ext.autodoc']
+
+    templates_path = ['_templates']
+
+    source_suffix = '.rst'
+
+    master_doc = 'index'
+
+    project = u'Katello CLI'
+
+    copyright = u'Red Hat, Inc.'
+
+    version = ''
+
+    release = ''
+
+    exclude_patterns = []
+
+    pygments_style = 'sphinx'
+
+    html_theme = 'default'
+
+    html_static_path = ['_static']
+
+    htmlhelp_basename = 'KatelloCLIdoc'
+
+    latex_documents = [('index', 'KatelloCLI.tex', u'Katello CLI Documen...
+
+    man_pages = [('index', 'katellocli', u'Katello CLI Documentation', [...
diff --git a/create_product_test b/create_product_test
new file mode 100644
index 0000000..4d4c5a1
--- /dev/null
+++ b/create_product_test
@@ -0,0 +1,154 @@
+MODULE NAME
+    create_product_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_missing_prov_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class CreateTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_finds_provider(self)
+
+        test_creates_product(self)
+
+        test_discovers_repos(self)
+
+        test_creates_product_without_repositories_if_url_was_not_specified(self)
+
+        test_selects_repos(self)
+
+        test_create_repos(self)
+
+        Class variables
+
+            PROVIDER = 'provider'
+
+            PROVIDER_ID = '123'
+
+            ORGANIZATION = 'org'
+
+            PRODUCT = 'product1'
+
+            PRODUCT_ID = '123'
+
+            DESCRIPTION = 'description'
+
+            URL = 'http://localhost'
+
+            DISCOVERED_REPOS = ['url1', 'url2']
+
+            ASSUMEYES = True
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/create_repo_test b/create_repo_test
new file mode 100644
index 0000000..2af3c4e
--- /dev/null
+++ b/create_repo_test
@@ -0,0 +1,323 @@
+MODULE NAME
+    create_repo_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_missing_url_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class RepoDiscoveryTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_performs_pulp_repo_discovery(self)
+
+        test_polls_pulp(self)
+
+        test_exit_when_no_repos_were_discovered(self)
+
+        Class variables
+
+            RESULT = {'result': ''}
+
+            DISCOVERY_TASK = {}
+
+            URL = 'http://localhost'
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class RepositorySelectionTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_q_forces_exit(self)
+
+        test_a_y_adds_all_discovered_repos(self)
+
+        test_1_y_adds_first_discovered_repo(self)
+
+        test_assumeyes_adds_all_discovered_repos(self)
+
+        Class variables
+
+            DISCOVERED_URLS = ['http://localhost/a/b/', 'http://localhos...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class RepositoryNameTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_replaces_slashes_with_underscores(self)
+
+        Class variables
+
+            NAME = 'REPO'
+
+            URL = 'http://localhost/a/b/'
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+            unittest.TestCase.tearDown(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class CreateRepositoryTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_create_repo_in_pulp(self)
+
+        test_creates_repos_in_pulp_for_all_urls(self)
+
+        Class variables
+
+            PRODUCT_ID = '123'
+
+            NAME = 'REPO'
+
+            URL = 'http://localhost/a/b/'
+
+            URL2 = 'http://localhost/a/c/'
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+            unittest.TestCase.tearDown(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class RawInputStub
+
+
+        __init__(self, input)
+
+        raw_input(self, prompt)
diff --git a/errata_list_test b/errata_list_test
new file mode 100644
index 0000000..94e6442
--- /dev/null
+++ b/errata_list_test
@@ -0,0 +1,181 @@
+MODULE NAME
+    errata_list_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_repo_with_missing_org_generates_error(self)
+
+        test_repo_with_missing_product_generates_error(self)
+
+        test_product_with_missing_org_generates_error(self)
+
+        test_repo_id_neither_org_provided_generates_error(self)
+
+        test_no_error_if_all_required_provided(self)
+
+        test_no_error_if_repo_id(self)
+
+        test_no_error_if_org_provided(self)
+
+        test_no_error_if_org_environment_and_product(self)
+
+        test_accept_type_filter(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class ErrataListTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_prints_products(self)
+
+        test_it_uses_locker_when_no_env_is_specified(self)
+
+        test_it_searches_for_env_id_when_env_is_specified(self)
+
+        test_it_searches_for_product_id_when_product_specified(self)
+
+        test_it_supports_filtering_by_type(self)
+
+        test_it_supports_filtering_by_severity(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            ENV = test_data.ENVS [0]
+
+            PRODUCT = test_data.PRODUCTS [0]
+
+            REPO = test_data.REPOS [0]
+
+            OPTIONS_BY_PRODUCT_AND_REPO = {'org': ORG ['name'], 'product...
+
+            OPTIONS_BY_TYPE = {'org': ORG ['name'], 'product': PRODUCT [...
+
+            OPTIONS_BY_ORG = {'org': ORG ['name']}
+
+            OPTIONS_BY_ORG_AND_PRODUCT = {'org': ORG ['name'], 'product'...
+
+            OPTIONS_BY_ENV = {'org': ORG ['name'], 'env': ENV ['name']}
+
+            OPTIONS_BY_SEVERITY = {'org': ORG ['name'], 'product': PRODU...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/filter_add_package_test b/filter_add_package_test
new file mode 100644
index 0000000..9f106b1
--- /dev/null
+++ b/filter_add_package_test
@@ -0,0 +1,155 @@
+MODULE NAME
+    filter_add_package_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_missing_package_id_generates_error(self)
+
+        test_no_error_if_org_and_name_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class FilterAddTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_calls_filter_info_api(self)
+
+        test_it_calls_filter_update_api(self)
+
+        Class variables
+
+            ORG = 'org'
+
+            NAME = 'filter'
+
+            FILTER1 = "filter1"
+
+            FILTER2 = "filter2"
+
+            FILTER3 = "filter3"
+
+            OPTIONS = {'org': ORG, 'name': NAME, 'package_id': FILTER1}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/filter_create_test b/filter_create_test
new file mode 100644
index 0000000..52ae746
--- /dev/null
+++ b/filter_create_test
@@ -0,0 +1,157 @@
+MODULE NAME
+    filter_create_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_org_and_name_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class FilterAddTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_parse_packges_returns_list_of_pacakge_ids(self)
+
+        test_parse_packages_strips_spaces(self)
+
+        test_it_uses_filter_create_api(self)
+
+        Class variables
+
+            ORG = 'org'
+
+            NAME = 'filter'
+
+            DESCRIPTION = 'description'
+
+            FILTER1 = "filter1"
+
+            FILTER2 = "filter2"
+
+            FILTER3 = "filter3"
+
+            OPTIONS = {'org': ORG, 'name': NAME, 'description': DESCRIPT...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/filter_delete_test b/filter_delete_test
new file mode 100644
index 0000000..c8e5c5a
--- /dev/null
+++ b/filter_delete_test
@@ -0,0 +1,145 @@
+MODULE NAME
+    filter_delete_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_org_and_name_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class FilterDeleteTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_uses_filter_delete_api(self)
+
+        Class variables
+
+            ORG = 'org'
+
+            FILTER = 'filter'
+
+            OPTIONS = {'org': ORG, 'name': FILTER}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/filter_info_test b/filter_info_test
new file mode 100644
index 0000000..cf58974
--- /dev/null
+++ b/filter_info_test
@@ -0,0 +1,147 @@
+MODULE NAME
+    filter_info_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_org_and_name_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class FilterAddTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_calls_filter_info_api(self)
+
+        test_package_list_as_string(self)
+
+        Class variables
+
+            ORG = 'org'
+
+            FILTER = 'filter'
+
+            OPTIONS = {'org': ORG, 'name': FILTER}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/filter_list_test b/filter_list_test
new file mode 100644
index 0000000..1ea6afa
--- /dev/null
+++ b/filter_list_test
@@ -0,0 +1,141 @@
+MODULE NAME
+    filter_list_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_no_error_if_org_and_product_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class FilterListTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_uses_filter_list_api(self)
+
+        Class variables
+
+            ORG = 'some_org'
+
+            OPTIONS = {'org': ORG}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/filter_remove_package_test b/filter_remove_package_test
new file mode 100644
index 0000000..786b89c
--- /dev/null
+++ b/filter_remove_package_test
@@ -0,0 +1,155 @@
+MODULE NAME
+    filter_remove_package_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_missing_package_id_generates_error(self)
+
+        test_no_error_if_org_and_name_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class FilterAddTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_calls_filter_info_api(self)
+
+        test_it_calls_filter_update_api(self)
+
+        Class variables
+
+            ORG = 'org'
+
+            NAME = 'filter'
+
+            FILTER1 = "filter1"
+
+            FILTER2 = "filter2"
+
+            FILTER3 = "filter3"
+
+            OPTIONS = {'org': ORG, 'name': NAME, 'package_id': FILTER2}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/katello b/katello
new file mode 100644
index 0000000..7ef3c06
--- /dev/null
+++ b/katello
@@ -0,0 +1,2 @@
+MODULE NAME
+    katello
diff --git a/katello.client b/katello.client
new file mode 100644
index 0000000..064d9d8
--- /dev/null
+++ b/katello.client
@@ -0,0 +1,2 @@
+MODULE NAME
+    katello.client
diff --git a/katello.client.api b/katello.client.api
new file mode 100644
index 0000000..efc1c16
--- /dev/null
+++ b/katello.client.api
@@ -0,0 +1,2 @@
+MODULE NAME
+    katello.client.api
diff --git a/katello.client.api.activation_key b/katello.client.api.activation_key
new file mode 100644
index 0000000..b5b5f7d
--- /dev/null
+++ b/katello.client.api.activation_key
@@ -0,0 +1,24 @@
+MODULE NAME
+    katello.client.api.activation_key
+
+
+CLASSES
+
+    class ActivationKeyAPI(katello.client.api.base.KatelloAPI)
+
+
+        activation_keys_by_organization(self, orgId, keyName=None)
+
+        activation_keys_by_environment(self, envId)
+
+        activation_key(self, keyId)
+
+        create(self, envId, name, description, templateId=None)
+
+        update(self, keyId, environmentId, name, description, templateId)
+
+        add_pool(self, keyId, poolid)
+
+        remove_pool(self, keyId, poolid)
+
+        delete(self, keyId)
diff --git a/katello.client.api.admin b/katello.client.api.admin
new file mode 100644
index 0000000..368b919
--- /dev/null
+++ b/katello.client.api.admin
@@ -0,0 +1,11 @@
+MODULE NAME
+    katello.client.api.admin
+
+
+CLASSES
+
+    class AdminAPI(katello.client.api.base.KatelloAPI)
+        Various administrative actions
+
+
+        crl_regen(self, query=None)
diff --git a/katello.client.api.base b/katello.client.api.base
new file mode 100644
index 0000000..fad59e8
--- /dev/null
+++ b/katello.client.api.base
@@ -0,0 +1,20 @@
+MODULE NAME
+    katello.client.api.base
+
+
+CLASSES
+
+    class KatelloAPI(object)
+        Base api class that allows an internal server object to be set
+        after instantiation.
+
+
+        update_dict(self, d, key, value)
+            Update value for key in fictionary only if the value is not
+            None.
+
+        Instance variables
+
+            server(self)
+
+        Inherited methods
diff --git a/katello.client.api.changeset b/katello.client.api.changeset
new file mode 100644
index 0000000..1616aa3
--- /dev/null
+++ b/katello.client.api.changeset
@@ -0,0 +1,22 @@
+MODULE NAME
+    katello.client.api.changeset
+
+
+CLASSES
+
+    class ChangesetAPI(katello.client.api.base.KatelloAPI)
+
+
+        changesets(self, orgName, envId)
+
+        changeset(self, csId)
+
+        changeset_by_name(self, orgName, envId, csName)
+
+        create(self, orgName, envId, name)
+
+        delete(self, orgName, envId, csId)
+
+        promote(self, orgName, envId, csId)
+
+        update_content(self, orgName, envId, csId, patch)
diff --git a/katello.client.api.content_view b/katello.client.api.content_view
new file mode 100644
index 0000000..38f46ac
--- /dev/null
+++ b/katello.client.api.content_view
@@ -0,0 +1,26 @@
+MODULE NAME
+    katello.client.api.content_view
+
+
+CLASSES
+
+    class ContentViewAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access content_view calls
+
+
+        content_views_by_org(self, org_id, env=None)
+
+        views_by_label_name_or_id(self, org_id, label=None, name=None,
+                                  vid=None)
+
+        show(self, org_name, view_id, environment_id=None)
+
+        content_view_by_label(self, org_id, view_label)
+
+        update(self, org_id, cv_id, label, description)
+
+        delete(self, org_id, cv_id)
+
+        promote(self, cv_id, env_id)
+
+        refresh(self, cv_id)
diff --git a/katello.client.api.content_view_definition b/katello.client.api.content_view_definition
new file mode 100644
index 0000000..ba998dd
--- /dev/null
+++ b/katello.client.api.content_view_definition
@@ -0,0 +1,38 @@
+MODULE NAME
+    katello.client.api.content_view_definition
+
+
+CLASSES
+
+    class ContentViewDefinitionAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access content_view calls
+
+
+        content_view_definitions_by_org(self, org_id)
+
+        cvd_by_label_or_name_or_id(self, org_id, label=None, name=None,
+                                   cvd_id=None)
+
+        show(self, org_id, cvd_id)
+
+        create(self, org_id, name, label, description, composite=False)
+
+        update(self, org, cvd_id, name, description)
+
+        delete(self, cvd_id)
+
+        publish(self, org_id, cvd_id, name, label=None, description=None)
+
+        clone(self, org, cvd_id, name, label=None, description=None)
+
+        products(self, org, cvd_id)
+
+        update_products(self, org, cvd, products)
+
+        repos(self, org, cvd_id)
+
+        update_repos(self, org, cvd, repos)
+
+        content_views(self, cvd_id)
+
+        update_content_views(self, cvd_id, views)
diff --git a/katello.client.api.distribution b/katello.client.api.distribution
new file mode 100644
index 0000000..b83fde6
--- /dev/null
+++ b/katello.client.api.distribution
@@ -0,0 +1,13 @@
+MODULE NAME
+    katello.client.api.distribution
+
+
+CLASSES
+
+    class DistributionAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access distributions
+
+
+        distributions_by_repo(self, repoId)
+
+        distribution(self, distribution_id)
diff --git a/katello.client.api.environment b/katello.client.api.environment
new file mode 100644
index 0000000..cc99ace
--- /dev/null
+++ b/katello.client.api.environment
@@ -0,0 +1,23 @@
+MODULE NAME
+    katello.client.api.environment
+
+
+CLASSES
+
+    class EnvironmentAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access environment calls
+
+
+        environments_by_org(self, orgId)
+
+        environment_by_org(self, orgId, envId)
+
+        environment_by_name(self, orgId, envName)
+
+        locker_by_org(self, orgId)
+
+        create(self, orgId, name, description, priorId)
+
+        update(self, orgId, envId, name, description, priorId)
+
+        delete(self, orgId, envId)
diff --git a/katello.client.api.errata b/katello.client.api.errata
new file mode 100644
index 0000000..a580160
--- /dev/null
+++ b/katello.client.api.errata
@@ -0,0 +1,13 @@
+MODULE NAME
+    katello.client.api.errata
+
+
+CLASSES
+
+    class ErrataAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access errata calls
+
+
+        errata_by_repo(self, repoId)
+
+        errata(self, errata_id)
diff --git a/katello.client.api.filter b/katello.client.api.filter
new file mode 100644
index 0000000..6dbe013
--- /dev/null
+++ b/katello.client.api.filter
@@ -0,0 +1,19 @@
+MODULE NAME
+    katello.client.api.filter
+
+
+CLASSES
+
+    class FilterAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access Filter Data
+
+
+        filters(self, org)
+
+        create(self, org, name, description, filter_list)
+
+        delete(self, org, name)
+
+        info(self, org, name)
+
+        update_packages(self, org, name, package_list)
diff --git a/katello.client.api.organization b/katello.client.api.organization
new file mode 100644
index 0000000..bd5c9db
--- /dev/null
+++ b/katello.client.api.organization
@@ -0,0 +1,19 @@
+MODULE NAME
+    katello.client.api.organization
+
+
+CLASSES
+
+    class OrganizationAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access Organization Data
+
+
+        create(self, name, description)
+
+        delete(self, name)
+
+        update(self, name, description)
+
+        organizations(self)
+
+        organization(self, name)
diff --git a/katello.client.api.package b/katello.client.api.package
new file mode 100644
index 0000000..99e9978
--- /dev/null
+++ b/katello.client.api.package
@@ -0,0 +1,13 @@
+MODULE NAME
+    katello.client.api.package
+
+
+CLASSES
+
+    class PackageAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access package calls
+
+
+        package(self, packageId)
+
+        packages_by_repo(self, repoId)
diff --git a/katello.client.api.ping b/katello.client.api.ping
new file mode 100644
index 0000000..9d351ac
--- /dev/null
+++ b/katello.client.api.ping
@@ -0,0 +1,11 @@
+MODULE NAME
+    katello.client.api.ping
+
+
+CLASSES
+
+    class PingAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access Organization Data
+
+
+        ping(self)
diff --git a/katello.client.api.product b/katello.client.api.product
new file mode 100644
index 0000000..3271517
--- /dev/null
+++ b/katello.client.api.product
@@ -0,0 +1,23 @@
+MODULE NAME
+    katello.client.api.product
+
+
+CLASSES
+
+    class ProductAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access environment calls
+
+
+        products_by_org(self, orgName, prodName=None)
+
+        products_by_env(self, orgName, envName)
+
+        products_by_provider(self, provId, prodName=None)
+
+        products(self)
+
+        product_by_name(self, orgName, prodName)
+
+        create(self, provId, name, description, url)
+
+        sync(self, prodId)
diff --git a/katello.client.api.provider b/katello.client.api.provider
new file mode 100644
index 0000000..48ec192
--- /dev/null
+++ b/katello.client.api.provider
@@ -0,0 +1,27 @@
+MODULE NAME
+    katello.client.api.provider
+
+
+CLASSES
+
+    class ProviderAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access repo specific calls
+
+
+        create(self, name, orgName, description=None, pType=None, url=None)
+
+        delete(self, name)
+
+        update(self, provId, name, description=None, url=None)
+
+        providers(self)
+
+        providers_by_org(self, orgId)
+
+        provider(self, provId)
+
+        provider_by_name(self, orgName, provName)
+
+        sync(self, provId)
+
+        import_manifest(self, provId, manifestFile)
diff --git a/katello.client.api.repo b/katello.client.api.repo
new file mode 100644
index 0000000..7f7682d
--- /dev/null
+++ b/katello.client.api.repo
@@ -0,0 +1,25 @@
+MODULE NAME
+    katello.client.api.repo
+
+
+CLASSES
+
+    class RepoAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access repositories
+
+
+        create(self, prod_id, name, url)
+
+        repos_by_org_env(self, orgName, envId)
+
+        repos_by_env_product(self, envId, productId)
+
+        repos_by_product(self, productId)
+
+        repo(self, repo_id)
+
+        sync(self, repo_id)
+
+        repo_discovery(self, url, repotype)
+
+        repo_discovery_status(self, discoveryTaskId)
diff --git a/katello.client.api.system b/katello.client.api.system
new file mode 100644
index 0000000..88f9c95
--- /dev/null
+++ b/katello.client.api.system
@@ -0,0 +1,19 @@
+MODULE NAME
+    katello.client.api.system
+
+
+CLASSES
+
+    class SystemAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access environment calls
+
+
+        register(self, name, org, envName, cp_type)
+
+        unregister(self, system_id)
+
+        system(self, system_id)
+
+        systems_by_org(self, orgId, query={})
+
+        systems_by_env(self, orgId, envName, query={})
diff --git a/katello.client.api.task_status b/katello.client.api.task_status
new file mode 100644
index 0000000..65c5bab
--- /dev/null
+++ b/katello.client.api.task_status
@@ -0,0 +1,10 @@
+MODULE NAME
+    katello.client.api.task_status
+
+
+CLASSES
+
+    class TaskStatusAPI(katello.client.api.base.KatelloAPI)
+
+
+        status(self, taskUuid)
diff --git a/katello.client.api.template b/katello.client.api.template
new file mode 100644
index 0000000..f861bee
--- /dev/null
+++ b/katello.client.api.template
@@ -0,0 +1,28 @@
+MODULE NAME
+    katello.client.api.template
+
+
+CLASSES
+
+    class TemplateAPI(katello.client.api.base.KatelloAPI)
+
+
+        templates(self, envId)
+
+        template(self, tplId)
+
+        template_by_name(self, envId, tplName)
+
+        import_tpl(self, envId, description, tplFile)
+
+        create(self, envId, name, description, parentId)
+
+        update(self, tplId, newName, description, parentId)
+
+        update_content(self, tplId, actionName, params)
+
+        promote(self, template_id)
+
+        promotion_status(self, task_id)
+
+        delete(self, template_id)
diff --git a/katello.client.api.user b/katello.client.api.user
new file mode 100644
index 0000000..3ba3dbe
--- /dev/null
+++ b/katello.client.api.user
@@ -0,0 +1,19 @@
+MODULE NAME
+    katello.client.api.user
+
+
+CLASSES
+
+    class UserAPI(katello.client.api.base.KatelloAPI)
+        Connection class to access User Data
+
+
+        create(self, name, pw, disabled)
+
+        delete(self, user_id)
+
+        update(self, user_id, pw, disabled)
+
+        users(self, query={})
+
+        user(self, user_id)
diff --git a/katello.client.api.utils b/katello.client.api.utils
new file mode 100644
index 0000000..4df176b
--- /dev/null
+++ b/katello.client.api.utils
@@ -0,0 +1,17 @@
+MODULE NAME
+    katello.client.api.utils
+
+
+FUNCTIONS
+
+    get_environment(orgName, envName=None)
+
+    get_product(orgName, prodName)
+
+    get_repo(orgName, prodName, repoName, envName=None)
+
+    get_provider(orgName, provName)
+
+    get_template(orgName, envName, tplName)
+
+    get_changeset(orgName, envName, csName)
diff --git a/katello.client.cli b/katello.client.cli
new file mode 100644
index 0000000..72d3ba3
--- /dev/null
+++ b/katello.client.cli
@@ -0,0 +1,2 @@
+MODULE NAME
+    katello.client.cli
diff --git a/katello.client.cli.admin b/katello.client.cli.admin
new file mode 100644
index 0000000..830319c
--- /dev/null
+++ b/katello.client.cli.admin
@@ -0,0 +1,7 @@
+MODULE NAME
+    katello.client.cli.admin
+
+
+CLASSES
+
+    class AdminCLI(katello.client.cli.base.KatelloCLI)
diff --git a/katello.client.cli.base b/katello.client.cli.base
new file mode 100644
index 0000000..58d1be5
--- /dev/null
+++ b/katello.client.cli.base
@@ -0,0 +1,94 @@
+MODULE NAME
+    katello.client.cli.base
+
+
+CLASSES
+
+    class OptionException(exceptions.Exception)
+        Exception to be used, when value of an option is not valid e.g. not
+        found
+
+        Inherited methods
+            exceptions.BaseException.__delattr__(...)
+            exceptions.BaseException.__getattribute__(...)
+            exceptions.BaseException.__getitem__(x, y)
+            exceptions.BaseException.__getslice__(x, i, j)
+            exceptions.Exception.__init__(...)
+            exceptions.Exception.__new__(T, S, *...)
+            exceptions.BaseException.__reduce__(...)
+            exceptions.BaseException.__repr__(x)
+            exceptions.BaseException.__setattr__(...)
+            exceptions.BaseException.__setstate__(...)
+            exceptions.BaseException.__str__(x)
+            exceptions.BaseException.__unicode__(...)
+
+    class KatelloError(exceptions.Exception)
+        User-friendly exception wrapper (used for stderr output).
+
+
+        __init__(self, message, exception)
+            x.__init__(...) initializes x; see x.__class__.__doc__ for
+            signature
+
+        __str__(self)
+            str(x)
+
+        Inherited methods
+            exceptions.BaseException.__delattr__(...)
+            exceptions.BaseException.__getattribute__(...)
+            exceptions.BaseException.__getitem__(x, y)
+            exceptions.BaseException.__getslice__(x, i, j)
+            exceptions.Exception.__new__(T, S, *...)
+            exceptions.BaseException.__reduce__(...)
+            exceptions.BaseException.__repr__(x)
+            exceptions.BaseException.__setattr__(...)
+            exceptions.BaseException.__setstate__(...)
+            exceptions.BaseException.__unicode__(...)
+
+    class KatelloCLI(object)
+        Katello command line tool class.
+
+
+        __init__(self)
+            x.__init__(...) initializes x; see x.__class__.__doc__ for
+            signature
+
+        usage(self)
+            Usage string.
+            Returns:
+                command's usage string
+            Return type:
+                str
+
+        __build_command_usage_lines(self, command)
+
+        add_command(self, name, command)
+            Add a command to this command line tool
+
+        remove_command(self, name)
+
+        setup_parser(self)
+            Add options to the command line parser.
+
+        setup_server(self)
+            Setup the active server connection.
+
+        setup_credentials(self)
+            Setup up request credentials with the active server.
+
+        error(self, exception, errorMsg=None)
+
+        command_names(self)
+
+        get_command(self, name)
+
+        extract_command(self, args)
+
+        main(self, args=sys.argv [1:])
+            Run this command.
+
+        Inherited methods
+
+VARIABLES
+
+    _log = getLogger(__name__)
diff --git a/katello.client.config b/katello.client.config
new file mode 100644
index 0000000..4e7280f
--- /dev/null
+++ b/katello.client.config
@@ -0,0 +1,58 @@
+MODULE NAME
+    katello.client.config
+
+
+CLASSES
+
+    class Config(object)
+        The katello client configuration. Works as a static singleton
+        class.
+
+        This Config class acts as a wrapper for ConfigParser so that each
+        class needing to access the config file doesn't need to keep track
+        of the file path. You can simply initialize Config with 'Config()'
+        and you have full access to a RawConfigParser with the config file
+        read into the object for manipulation by calling methods on
+        'Config.parser' .
+
+        For more detailed information on ConfigParser and its methods,
+        please see http://docs.python.org/library/configparser.html .
+
+        To save to the config file after making changes, call
+        'Config.save()' and the changes will be written to file. Please
+        note this only saves 'options' section of the file storing it in
+        the client-options.conf file.
+
+        Config throws an Exception if 'Config.save()' is called before
+        initializing the Config object.
+
+
+        __init__(self)
+            Initializes a RawConfigParser and reads the configuration file
+            into the object
+
+        Static methods
+
+            save()
+                Save the "options" section to the client-options.conf file.
+
+                Please note other settings (other sections) are not saved!
+
+            ensure_dir(f)
+
+        Class variables
+
+            FILE = 'client.conf'
+
+            PATH = os.path.join('/etc/katello', FILE)
+                The absolute path to the config directory.
+
+            USER = os.path.expanduser(os.path.join('~/.katello', FILE))
+                The path to an alternate configuration file within the
+                user's home.
+
+            USER_OPTIONS = os.path.expanduser(os.path.join('~/.katello',...
+
+            parser = None
+
+        Inherited methods
diff --git a/katello.client.constants b/katello.client.constants
new file mode 100644
index 0000000..a3c5156
--- /dev/null
+++ b/katello.client.constants
@@ -0,0 +1,13 @@
+MODULE NAME
+    katello.client.constants
+
+
+VARIABLES
+
+    STATUS_DETAIL_SUCCESS = ...
+
+    STATUS_DETAIL_FAIL = ...
+
+    STATUS_INFO = ...
+
+    SELECTION_QUERY = ...
diff --git a/katello.client.core b/katello.client.core
new file mode 100644
index 0000000..a42e65c
--- /dev/null
+++ b/katello.client.core
@@ -0,0 +1,2 @@
+MODULE NAME
+    katello.client.core
diff --git a/katello.client.core.activation_key b/katello.client.core.activation_key
new file mode 100644
index 0000000..0bb112b
--- /dev/null
+++ b/katello.client.core.activation_key
@@ -0,0 +1,107 @@
+MODULE NAME
+    katello.client.core.activation_key
+
+
+CLASSES
+
+    class ActivationKeyAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+        get_template_id(self, environmentId, templateName)
+
+    class List(ActivationKeyAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        get_keys_for_organization(self, orgName)
+
+        get_keys_for_environment(self, orgName, envName)
+
+        Class variables
+
+            description = _('list all activation keys')
+
+        Inherited methods
+            katello.client.core.activation_key.ActivationKeyAction.__init__(self)
+            katello.client.core.activation_key.ActivationKeyAction.get_template_id(self, environmentId, templateName)
+
+    class Info(ActivationKeyAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('show information about an activation key')
+
+        Inherited methods
+            katello.client.core.activation_key.ActivationKeyAction.__init__(self)
+            katello.client.core.activation_key.ActivationKeyAction.get_template_id(self, environmentId, templateName)
+
+    class Create(ActivationKeyAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('create an activation key')
+
+        Inherited methods
+            katello.client.core.activation_key.ActivationKeyAction.__init__(self)
+            katello.client.core.activation_key.ActivationKeyAction.get_template_id(self, environmentId, templateName)
+
+    class Update(ActivationKeyAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('update an activation key')
+
+        Inherited methods
+            katello.client.core.activation_key.ActivationKeyAction.__init__(self)
+            katello.client.core.activation_key.ActivationKeyAction.get_template_id(self, environmentId, templateName)
+
+    class Delete(ActivationKeyAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('delete an activation key')
+
+        Inherited methods
+            katello.client.core.activation_key.ActivationKeyAction.__init__(self)
+            katello.client.core.activation_key.ActivationKeyAction.get_template_id(self, environmentId, templateName)
+
+    class ActivationKey(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('activation key specific actions in the kate...
diff --git a/katello.client.core.base b/katello.client.core.base
new file mode 100644
index 0000000..aa29968
--- /dev/null
+++ b/katello.client.core.base
@@ -0,0 +1,184 @@
+MODULE NAME
+    katello.client.core.base
+
+
+CLASSES
+
+    class Command(object)
+        Command class representing a katello cli command
+
+
+        __init__(self)
+            x.__init__(...) initializes x; see x.__class__.__doc__ for
+            signature
+
+        usage(self)
+            Return a string showing the command's usage
+
+        __build_action_usage_lines(self, action)
+
+        description(self)
+            Return a string showing the command's description
+
+        add_action(self, name, action)
+            Add an action to this command
+
+        action_names(self)
+
+        get_action(self, name)
+
+        process_options(self, args)
+
+        extract_action(self, args)
+
+        main(self, args)
+            Main execution of a katello cli command This method parses
+            options sent to the command itself, looks up the corresponding
+            action, and calls that action's main()
+
+        Instance variables
+
+            cert_file
+                certificate file credential
+
+            key_file
+                private key file credential
+
+            name
+                command's name
+
+            parser
+                optparse.OptionParser instance
+
+            password
+                password credential
+
+            username
+                username credential
+
+        Inherited methods
+
+    class Action(object)
+        Action class representing a single action for a cli command
+
+
+        __init__(self)
+            x.__init__(...) initializes x; see x.__class__.__doc__ for
+            signature
+
+        usage(self)
+            Return a string for this action's usage
+
+        description(self)
+            Return a string for this action's description
+
+        get_option(self, opt)
+            Get an option from opts or from the config file Options from
+            opts take precedence.
+            Returns:
+                value of the option or None if the option is no present
+
+        has_option(self, opt)
+            Check if option is present
+
+        require_option(self, opt_dest)
+            Add option error if an option is not present.
+
+        option_specified(self, opt)
+
+        add_option_error(self, errorMsg)
+            Add option error to the error stack
+
+        setup_parser(self)
+            Add custom options to the parser
+
+        add_scheduled_time_option(self)
+            Adds a --when scheduled time option to the option parser
+
+        parse_scheduled_time_option(self)
+
+        run(self)
+            Action's functionality
+
+        check_options(self)
+            Add custom option requirements
+
+        output_mode(self)
+
+        process_options(self, args)
+            This method setups up the parser, parses the arguments, checks
+            options and prints argument errors.
+
+        extract_action(self, args)
+
+        require_credentials(self)
+            if True, credentials are required when calling the command.
+
+        error(self, errorMsg)
+
+        main(self, args)
+            Main execution of the action This method setups up the parser,
+            parses the arguments, and calls run() in a try/except block,
+            handling RestlibExceptions and general errors
+
+        Instance variables
+
+            args
+                arguments returned from parsing command line
+
+            name
+                action's name
+
+            opts
+                options returned from parsing command line
+
+            parser
+                optparse.OptionParser instance
+
+        Inherited methods
+
+    class KatelloOption(optparse.Option)
+
+        Class variables
+
+            TYPES = Option.TYPES+("bool",)
+
+            TYPE_CHECKER = copy(Option.TYPE_CHECKER)
+
+        Inherited methods
+            optparse.Option.__init__(self, *opts, **attrs)
+            optparse.Option.__repr__(self)
+            optparse.Option.__str__(self)
+            optparse.Option._check_action(self)
+            optparse.Option._check_callback(self)
+            optparse.Option._check_choice(self)
+            optparse.Option._check_const(self)
+            optparse.Option._check_dest(self)
+            optparse.Option._check_nargs(self)
+            optparse.Option._check_opt_strings(self, opts)
+            optparse.Option._check_type(self)
+            optparse.Option._set_attrs(self, attrs)
+            optparse.Option._set_opt_strings(self, opts)
+            optparse.Option.check_value(self, opt, value)
+            optparse.Option.convert_value(self, opt, value)
+            optparse.Option.get_opt_string(self)
+            optparse.Option.process(self, opt, value, values, parser)
+            optparse.Option.take_action(self, action, dest, opt, value, values, parser)
+            optparse.Option.takes_value(self)
+
+        Inherited class variables
+            optparse.Option.ACTIONS = "store", "store_const", "store_true", "store_false", "...
+            optparse.Option.ALWAYS_TYPED_ACTIONS = "store", "append"
+            optparse.Option.ATTRS = ['action', 'type', 'dest', 'default', 'nargs', 'const'...
+            optparse.Option.CHECK_METHODS = [_check_action, _check_type, _check_choice, _check_des...
+            optparse.Option.CONST_ACTIONS = "store_const", "append_const"
+            optparse.Option.STORE_ACTIONS = "store", "store_const", "store_true", "store_false", "...
+            optparse.Option.TYPED_ACTIONS = "store", "append", "callback"
+
+FUNCTIONS
+
+    check_bool(option, opt, value)
+
+VARIABLES
+
+    _log = getLogger(__name__)
diff --git a/katello.client.core.changeset b/katello.client.core.changeset
new file mode 100644
index 0000000..bd9f9c1
--- /dev/null
+++ b/katello.client.core.changeset
@@ -0,0 +1,192 @@
+MODULE NAME
+    katello.client.core.changeset
+
+
+CLASSES
+
+    class ChangesetAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class List(ChangesetAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list new changesets of an environment')
+
+        Inherited methods
+            katello.client.core.changeset.ChangesetAction.__init__(self)
+
+    class Info(ChangesetAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('detailed information about a changeset')
+
+        Inherited methods
+            katello.client.core.changeset.ChangesetAction.__init__(self)
+
+    class Create(ChangesetAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('create a new changeset for an environment')
+
+        Inherited methods
+            katello.client.core.changeset.ChangesetAction.__init__(self)
+
+    class UpdateContent(ChangesetAction)
+
+
+        __init__(self)
+
+        store_from_product(self, option, opt_str, value, parser)
+
+        store_item_with_product(self, option, opt_str, value, parser)
+
+        store_item(self, option, opt_str, value, parser)
+
+        setup_parser(self)
+
+        reset_items(self)
+
+        check_options(self)
+
+        run(self)
+
+        update_content(self, csId, patch, updateMethod)
+
+        Nested classes
+
+            class PatchBuilder(object)
+             |
+             |  Static methods
+             |
+             |      build_patch(action, itemBuilder, items)
+             |
+             |  Inherited methods
+
+            class PatchItemBuilder(object)
+             |
+             |
+             |  __init__(self, orgName, envName)
+             |      x.__init__(...) initializes x; see x.__class__.__doc__
+             |      for signature
+             |
+             |  product_id(self, options)
+             |
+             |  repo_id(self, options)
+             |
+             |  template_id(self, options)
+             |
+             |  Inherited methods
+
+            class AddPatchItemBuilder(PatchItemBuilder)
+             |
+             |
+             |  package(self, options)
+             |
+             |  product(self, options)
+             |
+             |  erratum(self, options)
+             |
+             |  repo(self, options)
+             |
+             |  template(self, options)
+             |
+             |  distro(self, options)
+             |
+             |  Inherited methods
+             |      katello.client.core.changeset.UpdateContent.PatchItemBuilder.__init__(self, orgName, envName)
+             |      katello.client.core.changeset.UpdateContent.PatchItemBuilder.product_id(self, options)
+             |      katello.client.core.changeset.UpdateContent.PatchItemBuilder.repo_id(self, options)
+             |      katello.client.core.changeset.UpdateContent.PatchItemBuilder.template_id(self, options)
+
+            class RemovePatchItemBuilder(PatchItemBuilder)
+             |
+             |
+             |  package(self, options)
+             |
+             |  product(self, options)
+             |
+             |  erratum(self, options)
+             |
+             |  repo(self, options)
+             |
+             |  template(self, options)
+             |
+             |  distro(self, options)
+             |
+             |  Inherited methods
+             |      katello.client.core.changeset.UpdateContent.PatchItemBuilder.__init__(self, orgName, envName)
+             |      katello.client.core.changeset.UpdateContent.PatchItemBuilder.product_id(self, options)
+             |      katello.client.core.changeset.UpdateContent.PatchItemBuilder.repo_id(self, options)
+             |      katello.client.core.changeset.UpdateContent.PatchItemBuilder.template_id(self, options)
+
+        Class variables
+
+            productDependentContent = ['package', 'erratum', 'repo', 'di...
+
+            productIndependentContent = ['product', 'template']
+
+            description = _('updates content of a changeset')
+
+    class Delete(ChangesetAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('deletes a changeset')
+
+        Inherited methods
+            katello.client.core.changeset.ChangesetAction.__init__(self)
+
+    class Promote(ChangesetAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('promotes a changeset to the next environment')
+
+        Inherited methods
+            katello.client.core.changeset.ChangesetAction.__init__(self)
+
+    class Changeset(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('changeset specific actions in the katello s...
diff --git a/katello.client.core.client b/katello.client.core.client
new file mode 100644
index 0000000..b343222
--- /dev/null
+++ b/katello.client.core.client
@@ -0,0 +1,64 @@
+MODULE NAME
+    katello.client.core.client
+
+
+CLASSES
+
+    class ClientAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class Remember(ClientAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('save an option to the client config')
+
+        Inherited methods
+            katello.client.core.client.ClientAction.__init__(self)
+
+    class Forget(ClientAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('remove an option from the client config')
+
+        Inherited methods
+            katello.client.core.client.ClientAction.__init__(self)
+
+    class SavedOptions(ClientAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list options saved in the client config')
+
+        Inherited methods
+            katello.client.core.client.ClientAction.__init__(self)
+
+    class Client(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('client specific actions in the katello serv...
diff --git a/katello.client.core.distribution b/katello.client.core.distribution
new file mode 100644
index 0000000..91b53cd
--- /dev/null
+++ b/katello.client.core.distribution
@@ -0,0 +1,48 @@
+MODULE NAME
+    katello.client.core.distribution
+
+
+CLASSES
+
+    class DistributionAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class List(DistributionAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list distributions in a repository')
+
+        Inherited methods
+            katello.client.core.distribution.DistributionAction.__init__(self)
+
+    class Info(DistributionAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list information about a distribution')
+
+        Inherited methods
+            katello.client.core.distribution.DistributionAction.__init__(self)
+
+    class Distribution(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('repo specific actions in the katello server')
diff --git a/katello.client.core.environment b/katello.client.core.environment
new file mode 100644
index 0000000..abc88f7
--- /dev/null
+++ b/katello.client.core.environment
@@ -0,0 +1,103 @@
+MODULE NAME
+    katello.client.core.environment
+
+
+CLASSES
+
+    class EnvironmentAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+        get_prior_id(self, orgName, priorName)
+
+    class List(EnvironmentAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list known environments')
+
+        Inherited methods
+            katello.client.core.environment.EnvironmentAction.__init__(self)
+            katello.client.core.environment.EnvironmentAction.get_prior_id(self, orgName, priorName)
+
+    class Info(EnvironmentAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list a specific envrionment')
+
+        Inherited methods
+            katello.client.core.environment.EnvironmentAction.__init__(self)
+            katello.client.core.environment.EnvironmentAction.get_prior_id(self, orgName, priorName)
+
+    class Create(EnvironmentAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('create an environment')
+
+        Inherited methods
+            katello.client.core.environment.EnvironmentAction.__init__(self)
+            katello.client.core.environment.EnvironmentAction.get_prior_id(self, orgName, priorName)
+
+    class Update(EnvironmentAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('update an environment')
+
+        Inherited methods
+            katello.client.core.environment.EnvironmentAction.__init__(self)
+            katello.client.core.environment.EnvironmentAction.get_prior_id(self, orgName, priorName)
+
+    class Delete(EnvironmentAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('delete an environment')
+
+        Inherited methods
+            katello.client.core.environment.EnvironmentAction.__init__(self)
+            katello.client.core.environment.EnvironmentAction.get_prior_id(self, orgName, priorName)
+
+    class Environment(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('environment specific actions in the katello...
diff --git a/katello.client.core.errata b/katello.client.core.errata
new file mode 100644
index 0000000..3994595
--- /dev/null
+++ b/katello.client.core.errata
@@ -0,0 +1,64 @@
+MODULE NAME
+    katello.client.core.errata
+
+
+CLASSES
+
+    class ErrataAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class List(ErrataAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list all errata for a repo')
+
+        Inherited methods
+            katello.client.core.errata.ErrataAction.__init__(self)
+
+    class SystemErrata(ErrataAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _("list errata for a system")
+
+        Inherited methods
+            katello.client.core.errata.ErrataAction.__init__(self)
+
+    class Info(ErrataAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('information about an errata')
+
+        Inherited methods
+            katello.client.core.errata.ErrataAction.__init__(self)
+
+    class Errata(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('errata specific actions in the katello serv...
diff --git a/katello.client.core.filters b/katello.client.core.filters
new file mode 100644
index 0000000..9d7a505
--- /dev/null
+++ b/katello.client.core.filters
@@ -0,0 +1,116 @@
+MODULE NAME
+    katello.client.core.filters
+
+
+CLASSES
+
+    class FilterAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class List(FilterAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list all filters')
+
+        Inherited methods
+            katello.client.core.filters.FilterAction.__init__(self)
+
+    class Create(FilterAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        parse_packages(self, packages)
+
+        Class variables
+
+            description = _('create a filter')
+
+        Inherited methods
+            katello.client.core.filters.FilterAction.__init__(self)
+
+    class Delete(FilterAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('delete a filter')
+
+        Inherited methods
+            katello.client.core.filters.FilterAction.__init__(self)
+
+    class Info(FilterAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        package_list_as_string(self, package_list)
+
+        Class variables
+
+            description = _('filter info')
+
+        Inherited methods
+            katello.client.core.filters.FilterAction.__init__(self)
+
+    class AddPackage(FilterAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('Add a package to filter')
+
+        Inherited methods
+            katello.client.core.filters.FilterAction.__init__(self)
+
+    class RemovePackage(FilterAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('Remove a package from filter')
+
+        Inherited methods
+            katello.client.core.filters.FilterAction.__init__(self)
+
+    class Filter(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('filter specific actions in the katello serv...
diff --git a/katello.client.core.organization b/katello.client.core.organization
new file mode 100644
index 0000000..5b189c3
--- /dev/null
+++ b/katello.client.core.organization
@@ -0,0 +1,131 @@
+MODULE NAME
+    katello.client.core.organization
+
+
+CLASSES
+
+    class OrganizationAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class List(OrganizationAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('list all known organizations')
+
+        Inherited methods
+            katello.client.core.organization.OrganizationAction.__init__(self)
+
+    class Create(OrganizationAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('create an organization')
+
+        Inherited methods
+            katello.client.core.organization.OrganizationAction.__init__(self)
+
+    class Info(OrganizationAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list information about an organization')
+
+        Inherited methods
+            katello.client.core.organization.OrganizationAction.__init__(self)
+
+    class Delete(OrganizationAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('delete an organization')
+
+        Inherited methods
+            katello.client.core.organization.OrganizationAction.__init__(self)
+
+    class Update(OrganizationAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('update an organization')
+
+        Inherited methods
+            katello.client.core.organization.OrganizationAction.__init__(self)
+
+    class GenerateDebugCert(OrganizationAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('generate and show ueber certificate')
+
+        Inherited methods
+            katello.client.core.organization.OrganizationAction.__init__(self)
+
+    class ShowSubscriptions(OrganizationAction)
+
+
+        __init__(self)
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        sla(self, pool)
+
+        convert_timestamp(self, timestamp_field)
+
+        extract_sla_from_product(self, p)
+
+        displayable_pool(self, pool)
+
+        Class variables
+
+            description = _('show subscriptions')
+
+    class Organization(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('organization specific actions in the katell...
diff --git a/katello.client.core.package b/katello.client.core.package
new file mode 100644
index 0000000..2e3aa82
--- /dev/null
+++ b/katello.client.core.package
@@ -0,0 +1,48 @@
+MODULE NAME
+    katello.client.core.package
+
+
+CLASSES
+
+    class PackageAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class Info(PackageAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list information about a package')
+
+        Inherited methods
+            katello.client.core.package.PackageAction.__init__(self)
+
+    class List(PackageAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list packages in a repository')
+
+        Inherited methods
+            katello.client.core.package.PackageAction.__init__(self)
+
+    class Package(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('package specific actions in the katello ser...
diff --git a/katello.client.core.packagegroup b/katello.client.core.packagegroup
new file mode 100644
index 0000000..aaf922b
--- /dev/null
+++ b/katello.client.core.packagegroup
@@ -0,0 +1,84 @@
+MODULE NAME
+    katello.client.core.packagegroup
+
+
+CLASSES
+
+    class PackageGroupAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class List(PackageGroupAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list available package groups')
+
+        Inherited methods
+            katello.client.core.packagegroup.PackageGroupAction.__init__(self)
+
+    class Info(PackageGroupAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            name = "info"
+
+            description = _('lookup information for a package group')
+
+        Inherited methods
+            katello.client.core.packagegroup.PackageGroupAction.__init__(self)
+
+    class CategoryList(PackageGroupAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list available package groups categories')
+
+        Inherited methods
+            katello.client.core.packagegroup.PackageGroupAction.__init__(self)
+
+    class CategoryInfo(PackageGroupAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            name = "info"
+
+            description = _('lookup information for a package group')
+
+        Inherited methods
+            katello.client.core.packagegroup.PackageGroupAction.__init__(self)
+
+    class PackageGroup(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('package group specific actions in the katel...
diff --git a/katello.client.core.ping b/katello.client.core.ping
new file mode 100644
index 0000000..a2d5bef
--- /dev/null
+++ b/katello.client.core.ping
@@ -0,0 +1,49 @@
+MODULE NAME
+    katello.client.core.ping
+
+
+CLASSES
+
+    class PingAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class Status(PingAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        __returnCode(self, status)
+
+            Creates a return code according to returned statuses.
+            Error codes (combination by bitwise or):
+                candlepin:      2
+                candlepin_auth: 4
+                pulp:           8
+                pulp_auth:     16
+
+        __statusToList(self, status)
+
+        __sortedStatuses(self, status, reverse=False)
+
+        __buildOverallStatusDetail(self, status)
+
+        __buildServiceStatusDetail(self, serviceName, serviceStatus)
+
+        Class variables
+
+            description = _('get the status of the katello server')
+
+        Inherited methods
+            katello.client.core.ping.PingAction.__init__(self)
+
+    class Ping(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('Check the status of the server')
diff --git a/katello.client.core.product b/katello.client.core.product
new file mode 100644
index 0000000..38fc03c
--- /dev/null
+++ b/katello.client.core.product
@@ -0,0 +1,221 @@
+MODULE NAME
+    katello.client.core.product
+
+
+CLASSES
+
+    class ProductAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class SingleProductAction(ProductAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        set_product_select_options(self, select_by_env=True)
+
+        check_product_select_options(self)
+
+        Class variables
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.product.ProductAction.__init__(self)
+
+    class List(ProductAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list known products')
+
+        Inherited methods
+            katello.client.core.product.ProductAction.__init__(self)
+
+    class Sync(SingleProductAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('synchronize a product')
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.product.ProductAction.__init__(self)
+            katello.client.core.product.SingleProductAction.check_options(self)
+            katello.client.core.product.SingleProductAction.check_product_select_options(self)
+            katello.client.core.product.SingleProductAction.set_product_select_options(self, select_by_env=True)
+            katello.client.core.product.SingleProductAction.setup_parser(self)
+
+    class CancelSync(SingleProductAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('cancel currently running synchronization')
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.product.ProductAction.__init__(self)
+            katello.client.core.product.SingleProductAction.check_options(self)
+            katello.client.core.product.SingleProductAction.check_product_select_options(self)
+            katello.client.core.product.SingleProductAction.set_product_select_options(self, select_by_env=True)
+            katello.client.core.product.SingleProductAction.setup_parser(self)
+
+    class Status(SingleProductAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('status of product\'s synchronization')
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.product.ProductAction.__init__(self)
+            katello.client.core.product.SingleProductAction.check_options(self)
+            katello.client.core.product.SingleProductAction.check_product_select_options(self)
+            katello.client.core.product.SingleProductAction.set_product_select_options(self, select_by_env=True)
+            katello.client.core.product.SingleProductAction.setup_parser(self)
+
+    class Promote(SingleProductAction)
+
+
+        check_options(self)
+
+        run(self)
+
+        create_cs_name(self)
+
+        Class variables
+
+            description = _('promote a product to an environment\n(creat...
+
+            select_by_env = True
+
+        Inherited methods
+            katello.client.core.product.ProductAction.__init__(self)
+            katello.client.core.product.SingleProductAction.check_product_select_options(self)
+            katello.client.core.product.SingleProductAction.set_product_select_options(self, select_by_env=True)
+            katello.client.core.product.SingleProductAction.setup_parser(self)
+
+    class Create(ProductAction)
+
+
+        __init__(self)
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        create_product_with_repos(self, provName, orgName, name,
+                                  description, url, assumeyes, nodiscovery)
+
+        Class variables
+
+            description = _('create new product to a custom provider')
+
+    class Delete(SingleProductAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('delete a product and its content')
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.product.ProductAction.__init__(self)
+            katello.client.core.product.SingleProductAction.check_options(self)
+            katello.client.core.product.SingleProductAction.check_product_select_options(self)
+            katello.client.core.product.SingleProductAction.set_product_select_options(self, select_by_env=True)
+            katello.client.core.product.SingleProductAction.setup_parser(self)
+
+    class ListFilters(SingleProductAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('list filters of a product')
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.product.ProductAction.__init__(self)
+            katello.client.core.product.SingleProductAction.check_options(self)
+            katello.client.core.product.SingleProductAction.check_product_select_options(self)
+            katello.client.core.product.SingleProductAction.set_product_select_options(self, select_by_env=True)
+            katello.client.core.product.SingleProductAction.setup_parser(self)
+
+    class AddFilter(SingleProductAction)
+
+
+        __init__(self)
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('add a filter to a product')
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.product.SingleProductAction.check_product_select_options(self)
+            katello.client.core.product.SingleProductAction.set_product_select_options(self, select_by_env=True)
+
+    class DeleteFilter(SingleProductAction)
+
+
+        __init__(self)
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('delete a filter from a product')
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.product.SingleProductAction.check_product_select_options(self)
+            katello.client.core.product.SingleProductAction.set_product_select_options(self, select_by_env=True)
+
+    class Product(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('product specific actions in the katello ser...
diff --git a/katello.client.core.provider b/katello.client.core.provider
new file mode 100644
index 0000000..e78d432
--- /dev/null
+++ b/katello.client.core.provider
@@ -0,0 +1,147 @@
+MODULE NAME
+    katello.client.core.provider
+
+
+CLASSES
+
+    class ProviderAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class SingleProviderAction(ProviderAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        Inherited methods
+            katello.client.core.provider.ProviderAction.__init__(self)
+
+    class List(ProviderAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list all known providers')
+
+        Inherited methods
+            katello.client.core.provider.ProviderAction.__init__(self)
+
+    class Info(SingleProviderAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('list information about a provider')
+
+        Inherited methods
+            katello.client.core.provider.ProviderAction.__init__(self)
+            katello.client.core.provider.SingleProviderAction.check_options(self)
+            katello.client.core.provider.SingleProviderAction.setup_parser(self)
+
+    class Update(ProviderAction)
+
+
+        description(self)
+
+        __init__(self, create=False)
+
+        setup_parser(self)
+
+        check_options(self)
+
+        create(self, name, orgName, description, url)
+
+        update(self, name, orgName, newName, description, url)
+
+        run(self)
+
+    class Delete(SingleProviderAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('delete a provider')
+
+        Inherited methods
+            katello.client.core.provider.ProviderAction.__init__(self)
+            katello.client.core.provider.SingleProviderAction.check_options(self)
+            katello.client.core.provider.SingleProviderAction.setup_parser(self)
+
+    class Sync(SingleProviderAction)
+
+
+        run(self)
+
+        sync_provider(self, providerName, orgName)
+
+        Class variables
+
+            description = _('synchronize a provider')
+
+        Inherited methods
+            katello.client.core.provider.ProviderAction.__init__(self)
+            katello.client.core.provider.SingleProviderAction.check_options(self)
+            katello.client.core.provider.SingleProviderAction.setup_parser(self)
+
+    class CancelSync(SingleProviderAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('cancel currently running synchronization')
+
+        Inherited methods
+            katello.client.core.provider.ProviderAction.__init__(self)
+            katello.client.core.provider.SingleProviderAction.check_options(self)
+            katello.client.core.provider.SingleProviderAction.setup_parser(self)
+
+    class Status(SingleProviderAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('status of provider\'s synchronization')
+
+        Inherited methods
+            katello.client.core.provider.ProviderAction.__init__(self)
+            katello.client.core.provider.SingleProviderAction.check_options(self)
+            katello.client.core.provider.SingleProviderAction.setup_parser(self)
+
+    class ImportManifest(SingleProviderAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('import a manifest file')
+
+        Inherited methods
+            katello.client.core.provider.ProviderAction.__init__(self)
+
+    class Provider(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('provider specific actions in the katello se...
diff --git a/katello.client.core.repo b/katello.client.core.repo
new file mode 100644
index 0000000..6b9660c
--- /dev/null
+++ b/katello.client.core.repo
@@ -0,0 +1,270 @@
+MODULE NAME
+    katello.client.core.repo
+
+
+CLASSES
+
+    class RepoAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+        get_repo(self)
+
+    class SingleRepoAction(RepoAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        set_repo_select_options(self, select_by_env=True)
+
+        check_repo_select_options(self)
+
+        Class variables
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.repo.RepoAction.__init__(self)
+            katello.client.core.repo.RepoAction.get_repo(self)
+
+    class Create(RepoAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('create a repository at a specified URL')
+
+        Inherited methods
+            katello.client.core.repo.RepoAction.__init__(self)
+            katello.client.core.repo.RepoAction.get_repo(self)
+
+    class Discovery(RepoAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        discover_repositories(self, org_name, url)
+
+        select_repositories(self, repourls, assumeyes, raw_input=raw_input)
+
+        create_repositories(self, productid, name, selectedurls)
+
+        repository_name(self, name, parsedUrlPath)
+
+        __print_urls(self, repourls, selectedurls)
+
+        Class variables
+
+            description = _('discovery repositories contained within a U...
+
+        Inherited methods
+            katello.client.core.repo.RepoAction.__init__(self)
+            katello.client.core.repo.RepoAction.get_repo(self)
+
+    class Selection(list)
+
+
+        add_selection(self, urls)
+
+        Inherited methods
+            list.__add__(x, y)
+            list.__contains__(x, y)
+            list.__delitem__(x, y)
+            list.__delslice__(x, i, j)
+            list.__eq__(x, y)
+            list.__ge__(x, y)
+            list.__getattribute__(...)
+            list.__getitem__(x, y)
+            list.__getslice__(x, i, j)
+            list.__gt__(x, y)
+            list.__iadd__(x, y)
+            list.__imul__(x, y)
+            list.__init__()
+            list.__iter__(x)
+            list.__le__(x, y)
+            list.__len__(x)
+            list.__lt__(x, y)
+            list.__mul__(x, n)
+            list.__ne__(x, y)
+            list.__new__(T, S, *...)
+            list.__repr__(x)
+            list.__reversed__(L)
+            list.__rmul__(x, n)
+            list.__setitem__(x, i, y)
+            list.__setslice__(x, i, j, y)
+            list.__sizeof__(L)
+            list.append(L, object)
+            list.count(L, value)
+            list.extend(L, iterable)
+            list.index(...)
+            list.insert(L, index, object)
+            list.pop(L, index=...)
+            list.remove(L, value)
+            list.reverse(L)
+            list.sort(L, cmp=None, key=None, reverse=False)
+
+        Inherited class variables
+            list.__hash__ = None
+
+    class Status(SingleRepoAction)
+
+
+        run(self)
+
+        _format_error(self, errors)
+
+        Class variables
+
+            description = _('status information about a repository')
+
+            select_by_env = True
+
+        Inherited methods
+            katello.client.core.repo.RepoAction.__init__(self)
+            katello.client.core.repo.SingleRepoAction.check_options(self)
+            katello.client.core.repo.SingleRepoAction.check_repo_select_options(self)
+            katello.client.core.repo.RepoAction.get_repo(self)
+            katello.client.core.repo.SingleRepoAction.set_repo_select_options(self, select_by_env=True)
+            katello.client.core.repo.SingleRepoAction.setup_parser(self)
+
+    class Info(SingleRepoAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('information about a repository')
+
+            select_by_env = True
+
+        Inherited methods
+            katello.client.core.repo.RepoAction.__init__(self)
+            katello.client.core.repo.SingleRepoAction.check_options(self)
+            katello.client.core.repo.SingleRepoAction.check_repo_select_options(self)
+            katello.client.core.repo.RepoAction.get_repo(self)
+            katello.client.core.repo.SingleRepoAction.set_repo_select_options(self, select_by_env=True)
+            katello.client.core.repo.SingleRepoAction.setup_parser(self)
+
+    class Sync(SingleRepoAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('synchronize a repository')
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.repo.RepoAction.__init__(self)
+            katello.client.core.repo.SingleRepoAction.check_options(self)
+            katello.client.core.repo.SingleRepoAction.check_repo_select_options(self)
+            katello.client.core.repo.RepoAction.get_repo(self)
+            katello.client.core.repo.SingleRepoAction.set_repo_select_options(self, select_by_env=True)
+            katello.client.core.repo.SingleRepoAction.setup_parser(self)
+
+    class CancelSync(SingleRepoAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('cancel currently running synchronization of...
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.repo.RepoAction.__init__(self)
+            katello.client.core.repo.SingleRepoAction.check_options(self)
+            katello.client.core.repo.SingleRepoAction.check_repo_select_options(self)
+            katello.client.core.repo.RepoAction.get_repo(self)
+            katello.client.core.repo.SingleRepoAction.set_repo_select_options(self, select_by_env=True)
+            katello.client.core.repo.SingleRepoAction.setup_parser(self)
+
+    class Enable(SingleRepoAction)
+
+
+        description(self)
+
+        __init__(self, enable=True)
+
+        run(self)
+
+        Class variables
+
+            select_by_env = False
+
+        Inherited methods
+            katello.client.core.repo.SingleRepoAction.check_options(self)
+            katello.client.core.repo.SingleRepoAction.check_repo_select_options(self)
+            katello.client.core.repo.RepoAction.get_repo(self)
+            katello.client.core.repo.SingleRepoAction.set_repo_select_options(self, select_by_env=True)
+            katello.client.core.repo.SingleRepoAction.setup_parser(self)
+
+    class List(RepoAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list repos within an organization')
+
+        Inherited methods
+            katello.client.core.repo.RepoAction.__init__(self)
+            katello.client.core.repo.RepoAction.get_repo(self)
+
+    class Delete(SingleRepoAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('delete a repository')
+
+            select_by_env = True
+
+        Inherited methods
+            katello.client.core.repo.RepoAction.__init__(self)
+            katello.client.core.repo.SingleRepoAction.check_options(self)
+            katello.client.core.repo.SingleRepoAction.check_repo_select_options(self)
+            katello.client.core.repo.RepoAction.get_repo(self)
+            katello.client.core.repo.SingleRepoAction.set_repo_select_options(self, select_by_env=True)
+            katello.client.core.repo.SingleRepoAction.setup_parser(self)
+
+    class Repo(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('repo specific actions in the katello server')
+
+FUNCTIONS
+
+    format_sync_time(sync_time)
+
+    format_sync_state(state)
+
+VARIABLES
+
+    SYNC_STATES = {'waiting': _("Waiting"), 'running': _("Running"), 'er...
diff --git a/katello.client.core.shell_command b/katello.client.core.shell_command
new file mode 100644
index 0000000..f2f1902
--- /dev/null
+++ b/katello.client.core.shell_command
@@ -0,0 +1,20 @@
+MODULE NAME
+    katello.client.core.shell_command
+
+
+CLASSES
+
+    class ShellAction(katello.client.core.base.Action)
+
+
+        __init__(self, cli)
+
+        setup_parser(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('run the cli as a shell')
+
+            name = "shell"
diff --git a/katello.client.core.system b/katello.client.core.system
new file mode 100644
index 0000000..f86e2c2
--- /dev/null
+++ b/katello.client.core.system
@@ -0,0 +1,194 @@
+MODULE NAME
+    katello.client.core.system
+
+
+CLASSES
+
+    class SystemAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class List(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list systems within an organization')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class Info(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('display a system within an organization')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class InstalledPackages(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('display the installed packages of a system')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class Facts(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('display a the hardware facts of a system')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class Register(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        require_credentials(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('register a system')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class Unregister(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('unregister a system')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class Subscribe(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('subscribe a system to certificate')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class Subscriptions(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list subscriptions for a system')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class Unsubscribe(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('unsubscribe a system from certificate')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class Update(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('update a system')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class Report(SystemAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('systems report')
+
+        Inherited methods
+            katello.client.core.system.SystemAction.__init__(self)
+
+    class System(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('system specific actions in the katello serv...
diff --git a/katello.client.core.template b/katello.client.core.template
new file mode 100644
index 0000000..fb28811
--- /dev/null
+++ b/katello.client.core.template
@@ -0,0 +1,160 @@
+MODULE NAME
+    katello.client.core.template
+
+
+CLASSES
+
+    class TemplateAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+        get_parent_id(self, orgName, envName, parentName)
+
+    class List(TemplateAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list all templates')
+
+        Inherited methods
+            katello.client.core.template.TemplateAction.__init__(self)
+            katello.client.core.template.TemplateAction.get_parent_id(self, orgName, envName, parentName)
+
+    class Info(TemplateAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        _build_nvrea(self, package)
+
+        Class variables
+
+            description = _('list information about a template')
+
+        Inherited methods
+            katello.client.core.template.TemplateAction.__init__(self)
+            katello.client.core.template.TemplateAction.get_parent_id(self, orgName, envName, parentName)
+
+    class Import(TemplateAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        open_file(self, path)
+
+        Class variables
+
+            description = _('create a template file and import data')
+
+        Inherited methods
+            katello.client.core.template.TemplateAction.__init__(self)
+            katello.client.core.template.TemplateAction.get_parent_id(self, orgName, envName, parentName)
+
+    class Export(TemplateAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        open_file(self, path)
+
+        Class variables
+
+            description = _('export the template into the file')
+
+            supported_formats = ['json', 'tdl']
+
+        Inherited methods
+            katello.client.core.template.TemplateAction.__init__(self)
+            katello.client.core.template.TemplateAction.get_parent_id(self, orgName, envName, parentName)
+
+    class Create(TemplateAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('create an empty template file')
+
+        Inherited methods
+            katello.client.core.template.TemplateAction.__init__(self)
+            katello.client.core.template.TemplateAction.get_parent_id(self, orgName, envName, parentName)
+
+    class Update(TemplateAction)
+
+
+        __init__(self)
+
+        store_parameter_name(self, option, opt_str, value, parser)
+
+        store_parameter_value(self, option, opt_str, value, parser)
+
+        setup_parser(self)
+
+        check_options(self)
+
+        resetParameters(self)
+
+        getContent(self)
+
+        run(self)
+
+        productNamesToIds(self, orgName, productNames)
+
+        updateTemplate(self, tplId, name, desc, parentId)
+
+        updateContent(self, tplId, content)
+
+        Class variables
+
+            description = _('updates name and description of a template')
+
+        Inherited methods
+            katello.client.core.template.TemplateAction.get_parent_id(self, orgName, envName, parentName)
+
+    class Delete(TemplateAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('deletes a template')
+
+        Inherited methods
+            katello.client.core.template.TemplateAction.__init__(self)
+            katello.client.core.template.TemplateAction.get_parent_id(self, orgName, envName, parentName)
+
+    class Template(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('template specific actions in the katello se...
diff --git a/katello.client.core.user b/katello.client.core.user
new file mode 100644
index 0000000..9258664
--- /dev/null
+++ b/katello.client.core.user
@@ -0,0 +1,106 @@
+MODULE NAME
+    katello.client.core.user
+
+
+CLASSES
+
+    class UserAction(katello.client.core.base.Action)
+
+
+        __init__(self)
+
+    class List(UserAction)
+
+
+        run(self)
+
+        Class variables
+
+            description = _('list all known users')
+
+        Inherited methods
+            katello.client.core.user.UserAction.__init__(self)
+
+    class Create(UserAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('create user')
+
+        Inherited methods
+            katello.client.core.user.UserAction.__init__(self)
+
+    class Info(UserAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('list information about user')
+
+        Inherited methods
+            katello.client.core.user.UserAction.__init__(self)
+
+    class Delete(UserAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('delete user')
+
+        Inherited methods
+            katello.client.core.user.UserAction.__init__(self)
+
+    class Update(UserAction)
+
+
+        setup_parser(self)
+
+        check_options(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('update an user')
+
+        Inherited methods
+            katello.client.core.user.UserAction.__init__(self)
+
+    class Report(UserAction)
+
+
+        setup_parser(self)
+
+        run(self)
+
+        Class variables
+
+            description = _('user report')
+
+        Inherited methods
+            katello.client.core.user.UserAction.__init__(self)
+
+    class User(katello.client.core.base.Command)
+
+        Class variables
+
+            description = _('user specific actions in the katello server')
diff --git a/katello.client.core.utils b/katello.client.core.utils
new file mode 100644
index 0000000..3db4025
--- /dev/null
+++ b/katello.client.core.utils
@@ -0,0 +1,210 @@
+MODULE NAME
+    katello.client.core.utils
+
+
+CLASSES
+
+    class Printer
+        Class for unified printing of the CLI output.
+
+
+        __init__(self, output_mode, delimiter="")
+
+        setHeader(self, heading)
+
+        setOutputMode(self, output_mode)
+
+        _printDivLine(self, width)
+
+        _printHeader(self, heading, grep_mode, widths={})
+            Print a fancy header to stdout.
+
+        _getTermWidth(self)
+
+        _attrToName(self, attr_name)
+            Convert attribute name to display name. oraganization_id ->
+            Organization Id
+
+        addColumn(self, attr_name, name=None, multiline=False,
+                  show_in_grep=True, time_format=False, value='')
+            Add column to display
+
+        _printItem(self, item, indent="")
+            Print item from a list on number of lines
+
+        _printItemGrep(self, item, widths={})
+            Print item of a list on single line in grep mode
+
+        _calculateGrepWidths(self, items)
+
+        u_str(self, value)
+            Casts value to string unless it's unicode. There is a problem
+            using str on unicode values.
+
+        _minColumnWidth(self)
+
+        _getRandomNumber(self)
+
+        printItem(self, item, indent="")
+            Print one data item
+
+        printItems(self, items, indent="")
+            Print collection of data items
+
+        Class variables
+
+            OUTPUT_FORCE_NONE = 0
+
+            OUTPUT_FORCE_GREP = 1
+
+            OUTPUT_FORCE_VERBOSE = 2
+
+    class SystemExitRequest(exceptions.Exception)
+        Exception to indicate a system exit request. Introduced to The
+        arguments are [0] the response status as an integer and [1] a list
+        of error messages.
+
+        Inherited methods
+            exceptions.BaseException.__delattr__(...)
+            exceptions.BaseException.__getattribute__(...)
+            exceptions.BaseException.__getitem__(x, y)
+            exceptions.BaseException.__getslice__(x, i, j)
+            exceptions.Exception.__init__(...)
+            exceptions.Exception.__new__(T, S, *...)
+            exceptions.BaseException.__reduce__(...)
+            exceptions.BaseException.__repr__(x)
+            exceptions.BaseException.__setattr__(...)
+            exceptions.BaseException.__setstate__(...)
+            exceptions.BaseException.__str__(x)
+            exceptions.BaseException.__unicode__(...)
+
+    class Spinner(threading.Thread)
+        Spinner shows nice cli "spinner" while function is executing.
+
+        Each spinner instance can be started only once. Typical usage:
+
+        s = Spinner() s.start() ... s.stop() s.join()
+
+
+        __init__(self, msg="")
+            x.__init__(...) initializes x; see x.__class__.__doc__ for
+            signature
+
+        _putMessage(self)
+
+        _eraseMessage(self)
+
+        _putChar(self, char)
+
+        _resetCaret(self)
+
+        _eraseSpinner(self)
+
+        run(self)
+
+        stop(self)
+
+        Inherited methods
+            threading.Thread.__repr__(self)
+            threading._Verbose._note(self, *args)
+            threading.Thread._set_daemon(self)
+            threading.Thread._set_ident(self)
+            threading.Thread.daemon(self, daemonic)
+            threading.Thread.getName(self)
+            threading.Thread.ident(self)
+            threading.Thread.isAlive(self)
+            threading.Thread.isDaemon(self)
+            threading.Thread.is_alive(self)
+            threading.Thread.join(self, timeout=None)
+            threading.Thread.name(self, name)
+            threading.Thread.setDaemon(self, daemonic)
+            threading.Thread.setName(self, name)
+            threading.Thread.start(self)
+
+    class ProgressBar
+
+
+        updateProgress(self, progress)
+
+        done(self)
+
+    class AsyncTask
+
+
+        __init__(self, task)
+
+        update(self)
+
+        get_progress(self)
+
+        is_running(self)
+
+        finished(self)
+
+        failed(self)
+
+        succeeded(self)
+
+        subtask_count(self)
+
+        total_size(self)
+
+        total_count(self)
+
+        size_left(self)
+
+        items_left(self)
+
+        errors(self)
+
+        _get_progress_sum(self, name)
+
+        is_multiple(self)
+
+        get_hashes(self)
+
+        get_subtasks(self)
+
+        Class variables
+
+            _tasks = []
+
+FUNCTIONS
+
+    is_valid_record(rec)
+        Checks if record returned from server has been saved.
+
+    indent_text(text, indent="\t")
+
+    text_to_line(text, glue=" ")
+
+    system_exit(code, msgs=None)
+        Raise a system exit request exception with a return code and
+        optional message(s). Saves a few lines of code. Exception is
+        handled in command's main method. This allows not to exit the cli
+        but only skip out of the command when running in shell mode.
+
+    parse_tokens(tokenstring)
+        Parse string as if it was command line parameters.
+
+    get_abs_path(path)
+        Return absolute path with .. and ~ resolved
+
+    format_date(date, from_format="%Y-%m-%dT%H:%M:%SZ",
+                to_format="%Y/%m/%d %H:%M:%S")
+        Format standard rails timestamp to more human readable format
+
+    run_spinner_in_bg(function, arguments=(), message="")
+        Run spinner while a function is running.
+
+    wait_for_async_task(task)
+
+    run_async_task_with_status(task, progressBar)
+
+    progress(left, total)
+
+    convert_to_mime_type(type, default=None)
+
+    attachment_file_name(headers, default)
+
+    save_report(report, filename)
diff --git a/katello.client.i18n_optparse b/katello.client.i18n_optparse
new file mode 100644
index 0000000..5790c01
--- /dev/null
+++ b/katello.client.i18n_optparse
@@ -0,0 +1,116 @@
+MODULE NAME
+    katello.client.i18n_optparse
+
+DESCRIPTION
+    Make optparse friendlier to i18n/l10n
+
+    Just use this instead of optparse, the interface should be the same.
+
+    For some backgorund, see: http://bugs.python.org/issue4319
+
+
+CLASSES
+
+    class OptionParserExitError(exceptions.Exception)
+        Exception to indicate exit call from OptionParser. Takes error code
+        as it's only argument.
+
+        Inherited methods
+            exceptions.BaseException.__delattr__(...)
+            exceptions.BaseException.__getattribute__(...)
+            exceptions.BaseException.__getitem__(x, y)
+            exceptions.BaseException.__getslice__(x, i, j)
+            exceptions.Exception.__init__(...)
+            exceptions.Exception.__new__(T, S, *...)
+            exceptions.BaseException.__reduce__(...)
+            exceptions.BaseException.__repr__(x)
+            exceptions.BaseException.__setattr__(...)
+            exceptions.BaseException.__setstate__(...)
+            exceptions.BaseException.__str__(x)
+            exceptions.BaseException.__unicode__(...)
+
+    class OptionParser(optparse.OptionParser)
+
+
+        print_help(self)
+            print_help(file : file = stdout)
+
+            Print an extended help message, listing all options and any
+            help text provided with them, to 'file' (default stdout).
+
+        exit(self, status=0, msg=None)
+            Overridden method for means of CLI. Doesn't exit the whole
+            script but raises OptionParserExitError
+
+        error(self, errorMsg)
+            Print usage, one or more error messages and call exit.
+
+        get_option_by_dest(self, dest)
+
+        get_options(self)
+
+        get_long_options(self)
+
+        get_short_options(self)
+
+        Class variables
+
+            displayed_help = False
+
+        Inherited methods
+            optparse.OptionParser.__init__(self, usage=None, option_list=None, option_class=Option,
+                version=None, conflict_handler="error", description=None,
+                formatter=None, add_help_option=True, prog=None,
+                epilog=None)
+            optparse.OptionParser._add_help_option(self)
+            optparse.OptionParser._add_version_option(self)
+            optparse.OptionContainer._check_conflict(self, option)
+            optparse.OptionParser._create_option_list(self)
+            optparse.OptionContainer._create_option_mappings(self)
+            optparse.OptionParser._get_all_options(self)
+            optparse.OptionParser._get_args(self, args)
+            optparse.OptionParser._get_encoding(self, file)
+            optparse.OptionParser._init_parsing_state(self)
+            optparse.OptionParser._match_long_opt(self, opt)
+            optparse.OptionParser._populate_option_list(self, option_list, add_help=True)
+            optparse.OptionParser._process_args(self, largs, rargs, values)
+            optparse.OptionParser._process_long_opt(self, rargs, values)
+            optparse.OptionParser._process_short_opts(self, rargs, values)
+            optparse.OptionContainer._share_option_mappings(self, parser)
+            optparse.OptionContainer.add_option(Option)
+            optparse.OptionParser.add_option_group(self, *args, **kwargs)
+            optparse.OptionContainer.add_options(self, option_list)
+            optparse.OptionParser.check_values(self, values, args)
+            optparse.OptionParser.destroy(self)
+            optparse.OptionParser.disable_interspersed_args(self)
+            optparse.OptionParser.enable_interspersed_args(self)
+            optparse.OptionParser.expand_prog_name(self, s)
+            optparse.OptionContainer.format_description(self, formatter)
+            optparse.OptionParser.format_epilog(self, formatter)
+            optparse.OptionParser.format_help(self, formatter=None)
+            optparse.OptionParser.format_option_help(self, formatter=None)
+            optparse.OptionParser.get_default_values(self)
+            optparse.OptionParser.get_description(self)
+            optparse.OptionContainer.get_option(self, opt_str)
+            optparse.OptionParser.get_option_group(self, opt_str)
+            optparse.OptionParser.get_prog_name(self)
+            optparse.OptionParser.get_usage(self)
+            optparse.OptionParser.get_version(self)
+            optparse.OptionContainer.has_option(self, opt_str)
+            optparse.OptionParser.parse_args(self, args=None, values=None)
+            optparse.OptionParser.print_usage(self, file=None)
+            optparse.OptionParser.print_version(self, file=None)
+            optparse.OptionContainer.remove_option(self, opt_str)
+            optparse.OptionContainer.set_conflict_handler(self, handler)
+            optparse.OptionParser.set_default(self, dest, value)
+            optparse.OptionParser.set_defaults(self, **kwargs)
+            optparse.OptionContainer.set_description(self, description)
+            optparse.OptionParser.set_process_default_values(self, process)
+            optparse.OptionParser.set_usage(self, usage)
+
+        Inherited class variables
+            optparse.OptionParser.standard_option_list = []
+
+VARIABLES
+
+    _ = gettext.gettext
diff --git a/katello.client.logutil b/katello.client.logutil
new file mode 100644
index 0000000..82c795a
--- /dev/null
+++ b/katello.client.logutil
@@ -0,0 +1,35 @@
+MODULE NAME
+    katello.client.logutil
+
+
+FUNCTIONS
+
+    __logdir()
+
+    getLogger(name)
+
+VARIABLES
+
+    USRDIR = '~/.katello'
+
+    LOGDIR = '/var/log/katello'
+
+    LOGFILE = 'client.log'
+
+    TIME = '%(asctime)s'
+
+    LEVEL = ' [%(levelname)s]'
+
+    THREAD = '[%(threadName)s]'
+
+    FILE = ' @ %(filename)s'
+
+    LINE = ':%(lineno)d'
+
+    MSG = ' - %(message)s'
+
+    FUNCTION = ''
+
+    FMT = ''.join((TIME, LEVEL, THREAD, FUNCTION, FILE, LINE, MSG,))
+
+    handler = None
diff --git a/katello.client.server b/katello.client.server
new file mode 100644
index 0000000..e1d700a
--- /dev/null
+++ b/katello.client.server
@@ -0,0 +1,285 @@
+MODULE NAME
+    katello.client.server
+
+
+CLASSES
+
+    class ServerRequestError(exceptions.Exception)
+        Exception to indicate a less than favorable response from the
+        server. The arguments are [0] the response status as an integer and
+        [1] the response message as a dict, if we managed to decode from
+        json, or a str if we didn't [2] potentially a traceback, if the
+        server response was a python error, otherwise it will be None
+
+        Inherited methods
+            exceptions.BaseException.__delattr__(...)
+            exceptions.BaseException.__getattribute__(...)
+            exceptions.BaseException.__getitem__(x, y)
+            exceptions.BaseException.__getslice__(x, i, j)
+            exceptions.Exception.__init__(...)
+            exceptions.Exception.__new__(T, S, *...)
+            exceptions.BaseException.__reduce__(...)
+            exceptions.BaseException.__repr__(x)
+            exceptions.BaseException.__setattr__(...)
+            exceptions.BaseException.__setstate__(...)
+            exceptions.BaseException.__str__(x)
+            exceptions.BaseException.__unicode__(...)
+
+    class Bytes(str)
+        Binary (non-json) PUT/POST request body wrapper.
+
+        Inherited methods
+            str.__add__(x, y)
+            str.__contains__(x, y)
+            str.__eq__(x, y)
+            str.__format__(S, format_spec)
+            str.__ge__(x, y)
+            str.__getattribute__(...)
+            str.__getitem__(x, y)
+            str.__getnewargs__(...)
+            str.__getslice__(x, i, j)
+            str.__gt__(x, y)
+            str.__hash__(x)
+            str.__le__(x, y)
+            str.__len__(x)
+            str.__lt__(x, y)
+            str.__mod__(x, y)
+            str.__mul__(x, n)
+            str.__ne__(x, y)
+            str.__new__(T, S, *...)
+            str.__repr__(x)
+            str.__rmod__(x, y)
+            str.__rmul__(x, n)
+            str.__sizeof__(S)
+            str.__str__(x)
+            str._formatter_field_name_split(...)
+            str._formatter_parser(...)
+            str.capitalize(S)
+            str.center(S, width, fillchar=...)
+            str.count(S, sub, start=..., end=...)
+            str.decode(S, encoding=..., errors=...)
+            str.encode(S, encoding=..., errors=...)
+            str.endswith(S, suffix, start=..., end=...)
+            str.expandtabs(S, tabsize=...)
+            str.find(S, sub, start=... , end=...)
+            str.format(S, *args, **kwargs)
+            str.index(S, sub, start=... , end=...)
+            str.isalnum(S)
+            str.isalpha(S)
+            str.isdigit(S)
+            str.islower(S)
+            str.isspace(S)
+            str.istitle(S)
+            str.isupper(S)
+            str.join(S, iterable)
+            str.ljust(S, width, fillchar=...)
+            str.lower(S)
+            str.lstrip(S, chars=...)
+            str.partition(S, sep)
+            str.replace(S, old, new, count=...)
+            str.rfind(S, sub, start=... , end=...)
+            str.rindex(S, sub, start=... , end=...)
+            str.rjust(S, width, fillchar=...)
+            str.rpartition(S, sep)
+            str.rsplit(S, sep=... , maxsplit=...)
+            str.rstrip(S, chars=...)
+            str.split(S, sep=... , maxsplit=...)
+            str.splitlines(S, keepends=...)
+            str.startswith(S, prefix, start=..., end=...)
+            str.strip(S, chars=...)
+            str.swapcase(S)
+            str.title(S)
+            str.translate(S, table, deletechars=...)
+            str.upper(S)
+            str.zfill(S, width)
+
+    class Server(object)
+        Base server class.
+
+
+        __init__(self, host, port=80, protocol='http', path_prefix='')
+            x.__init__(...) initializes x; see x.__class__.__doc__ for
+            signature
+
+        set_basic_auth_credentials(self, username, password)
+            Set username and password credentials for http basic auth
+
+        set_ssl_credentials(self, certfile, keyfile)
+            Set ssl certificate and public key credentials
+
+        set_kerberos_auth(self)
+            Set kerberos authentication
+
+        has_credentials_set(self)
+
+        DELETE(self, path, body=None)
+            Send a DELETE request to the katello server.
+            Returns:
+                tuple of the http response status and the response body
+            Return type:
+                (int, dict or None or str)
+
+        GET(self, path, queries=())
+            Send a GET request to the katello server.
+            Returns:
+                tuple of the http response status and the response body
+            Return type:
+                (int, dict or None or str)
+
+        HEAD(self, path)
+            Send a HEAD request to the katello server.
+            Returns:
+                tuple of the http response status and the response body
+            Return type:
+                (int, dict or None or str)
+
+        POST(self, path, body=None, multipart=False)
+            Send a POST request to the katello server.
+            Returns:
+                tuple of the http response status and the response body
+            Return type:
+                (int, dict or None or str)
+
+        PUT(self, path, body, multipart=False)
+            Send a PUT request to the katello server.
+            Returns:
+                tuple of the http response status and the response body
+            Return type:
+                (int, dict or None or str)
+
+        Instance variables
+
+            headers
+                dictionary of http headers to send in requests
+
+            host
+                host name of the katello server
+
+            path_prefix
+                mount point of the katello api (/katello/api)
+
+            port
+                port the katello server is listening on (443)
+
+            protocol
+                protocol the katello server is using (http, https)
+
+        Inherited methods
+
+    class KatelloServer(Server)
+        Katello server connection class.
+
+
+        __init__(self, host, port=443, protocol='https',
+                 path_prefix='/katello/api')
+            x.__init__(...) initializes x; see x.__class__.__doc__ for
+            signature
+
+        _http_connection(self)
+
+        _https_connection(self)
+
+        _connect(self)
+
+        _build_url(self, path, queries=())
+
+        _request(self, method, path, queries=(), body=None, multipart=False,
+                 customHeaders={})
+
+        _prepare_body(self, body, multipart)
+            Encode body according to needs as json or multipart
+            Returns:
+                tuple of the content type and the encoded body
+            Return type:
+                (string, string)
+
+        _process_response(self, response)
+            Try to parse the response
+            Returns:
+                tuple of the response status and response body
+            Return type:
+                (int, string)
+
+        _flatten_to_multipart(self, key, data)
+            Encode data recursively as if they were sent by http form
+            Returns:
+                list of tuples of the field name and field value
+            Return type:
+                [(string, string)]
+
+        _encode_multipart_formdata(self, data)
+            Encode data for httplib request
+            Returns:
+                tuple of the content type and encoded data
+            Return type:
+                (string, string)
+
+        _get_content_type(self, filename)
+            Guess content type from file name
+            Returns:
+                http content type
+            Return type:
+                string
+
+        set_basic_auth_credentials(self, username, password)
+            Set username and password credentials for http basic auth
+
+        set_ssl_credentials(self, certfile, keyfile)
+            Set ssl certificate and public key credentials
+
+        set_kerberos_auth(self)
+            Set kerberos authentication
+
+        has_credentials_set(self)
+
+        DELETE(self, path, body=None)
+            Send a DELETE request to the katello server.
+            Returns:
+                tuple of the http response status and the response body
+            Return type:
+                (int, dict or None or str)
+
+        GET(self, path, queries=(), customHeaders={})
+            Send a GET request to the katello server.
+            Returns:
+                tuple of the http response status and the response body
+            Return type:
+                (int, dict or None or str)
+
+        HEAD(self, path)
+            Send a HEAD request to the katello server.
+            Returns:
+                tuple of the http response status and the response body
+            Return type:
+                (int, dict or None or str)
+
+        POST(self, path, body=None, multipart=False, customHeaders={})
+            Send a POST request to the katello server.
+            Returns:
+                tuple of the http response status and the response body
+            Return type:
+                (int, dict or None or str)
+
+        PUT(self, path, body, multipart=False, customHeaders={})
+            Send a PUT request to the katello server.
+            Returns:
+                tuple of the http response status and the response body
+            Return type:
+                (int, dict or None or str)
+
+        Inherited methods
+
+        Inherited instance variables
+            katello.client.server.Server.headers
+            katello.client.server.Server.host
+            katello.client.server.Server.path_prefix
+            katello.client.server.Server.port
+            katello.client.server.Server.protocol
+
+FUNCTIONS
+
+    set_active_server(server)
+
+VARIABLES
+
+    active_server = None
diff --git a/katello.client.shell b/katello.client.shell
new file mode 100644
index 0000000..388bd45
--- /dev/null
+++ b/katello.client.shell
@@ -0,0 +1,92 @@
+MODULE NAME
+    katello.client.shell
+
+
+CLASSES
+
+    class KatelloShell(cmd.Cmd)
+
+
+        __init__(self, admin_cli)
+            Instantiate a line-oriented interpreter framework.
+
+            The optional argument 'completekey' is the readline name of a
+            completion key; it defaults to the Tab key. If completekey is
+            not None and the readline module is available, command
+            completion is done automatically. The optional arguments stdin
+            and stdout specify alternate input and output file objects; if
+            not specified, sys.stdin and sys.stdout are used.
+
+        do_quit(self, args)
+
+        do_exit(self, args)
+
+        precmd(self, line)
+            Hook method executed just before the command line is
+            interpreted, but after the input prompt is generated and
+            issued.
+
+        parseline(self, line)
+            Parse the line into a command name and a string containing the
+            arguments.  Returns a tuple containing (command, args, line).
+            'command' and 'args' may be None if the line couldn't be
+            parsed.
+
+        postcmd(self, stop, line)
+            Hook method executed just after a command dispatch is finished.
+
+        do_help(self, strArgs)
+
+        completeparams(self, text, line, *ignored)
+
+        completecommands(self, text, line, *ignored)
+
+        completenames(self, text, *ignored)
+
+        complete(self, text, state)
+            Return the next possible completion for 'text'.
+
+            If a command has not been entered, then complete against
+            command list. Otherwise try to call complete_<command> to get
+            list of completions.
+
+        remove_last_history_item(self)
+
+        Class variables
+
+            HISTORY_LENGTH = 1024
+
+            cmdqueue = []
+
+            completekey = 'tab'
+
+            stdout = sys.stdout
+
+            current_line = ''
+
+            emptyline = lambda self:
+
+        Inherited methods
+            cmd.Cmd.cmdloop(self, intro=None)
+            cmd.Cmd.columnize(self, list, displaywidth=80)
+            cmd.Cmd.complete_help(self, *args)
+            cmd.Cmd.completedefault(self, *ignored)
+            cmd.Cmd.default(self, line)
+            cmd.Cmd.get_names(self)
+            cmd.Cmd.onecmd(self, line)
+            cmd.Cmd.postloop(self)
+            cmd.Cmd.preloop(self)
+            cmd.Cmd.print_topics(self, header, cmds, cmdlen, maxcol)
+
+        Inherited class variables
+            cmd.Cmd.doc_header = "Documented commands (type help <topic>):"
+            cmd.Cmd.doc_leader = ""
+            cmd.Cmd.identchars = string.ascii_letters+ string.digits+ '_'
+            cmd.Cmd.intro = None
+            cmd.Cmd.lastcmd = ''
+            cmd.Cmd.misc_header = "Miscellaneous help topics:"
+            cmd.Cmd.nohelp = "*** No help on %s"
+            cmd.Cmd.prompt = '(Cmd) '
+            cmd.Cmd.ruler = '='
+            cmd.Cmd.undoc_header = "Undocumented commands:"
+            cmd.Cmd.use_rawinput = 1
diff --git a/organization_subscription_list_test b/organization_subscription_list_test
new file mode 100644
index 0000000..999c4dc
--- /dev/null
+++ b/organization_subscription_list_test
@@ -0,0 +1,147 @@
+MODULE NAME
+    organization_subscription_list_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_no_error_if_org_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class SubscriptionsListTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_finds_all_pools_for_organization(self)
+
+        test_extract_sla_from_product(self)
+
+        test_extract_sla_from_product_with_no_sla(self)
+
+        test_displayable_pool(self)
+
+        Class variables
+
+            ORGANIZATION = 'org'
+
+            OPTIONS = {'name': ORGANIZATION}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/packagegroup_category_info_test b/packagegroup_category_info_test
new file mode 100644
index 0000000..9a777e4
--- /dev/null
+++ b/packagegroup_category_info_test
@@ -0,0 +1,149 @@
+MODULE NAME
+    packagegroup_category_info_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_id_and_repoid_generates_error(self)
+
+        test_missing_id_generates_error(self)
+
+        test_missing_repoid_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class PackageGroupCategoryInfoTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_finds_package_groups_by_id(self)
+
+        test_it_prints_package_groups(self)
+
+        Class variables
+
+            REPO = test_data.REPOS [0]
+
+            PACKAGE_GROUP_CATEGORY = test_data.PACKAGE_GROUP_CATEGORIES [0]
+
+            OPTIONS = {'repo_id': REPO ['id'], 'id': PACKAGE_GROUP_CATEG...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/packagegroup_category_list_test b/packagegroup_category_list_test
new file mode 100644
index 0000000..1aabe84
--- /dev/null
+++ b/packagegroup_category_list_test
@@ -0,0 +1,143 @@
+MODULE NAME
+    packagegroup_category_list_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_repoid_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class PackageGroupCategoryListTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_finds_package_group_categories_by_repo(self)
+
+        test_it_prints_package_groups(self)
+
+        Class variables
+
+            REPO = test_data.REPOS [0]
+
+            OPTIONS = {'repo_id': REPO ['id'],}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/packagegroup_info_test b/packagegroup_info_test
new file mode 100644
index 0000000..c740a1d
--- /dev/null
+++ b/packagegroup_info_test
@@ -0,0 +1,149 @@
+MODULE NAME
+    packagegroup_info_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_id_and_repoid_generates_error(self)
+
+        test_missing_id_generates_error(self)
+
+        test_missing_repoid_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class PackageGroupInfoTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_finds_package_group_by_id(self)
+
+        test_it_prints_package_groups(self)
+
+        Class variables
+
+            REPO = test_data.REPOS [0]
+
+            PACKAGE_GROUP = test_data.PACKAGE_GROUPS [0]
+
+            OPTIONS = {'repo_id': REPO ['id'], 'id': PACKAGE_GROUP ['id'],}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/packagegroup_list_test b/packagegroup_list_test
new file mode 100644
index 0000000..91a1667
--- /dev/null
+++ b/packagegroup_list_test
@@ -0,0 +1,143 @@
+MODULE NAME
+    packagegroup_list_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_repoid_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class PackageGroupListTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_finds_package_groups_by_repo(self)
+
+        test_it_prints_package_groups(self)
+
+        Class variables
+
+            REPO = test_data.REPOS [0]
+
+            OPTIONS = {'repo_id': REPO ['id'],}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/ping_test b/ping_test
new file mode 100644
index 0000000..ca7f2ac
--- /dev/null
+++ b/ping_test
@@ -0,0 +1,75 @@
+MODULE NAME
+    ping_test
+
+
+CLASSES
+
+    class ProductListTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_calls_the_api(self)
+
+        test_it_returns_correct_error_code_when_all_systems_up(self)
+
+        test_it_returns_correct_error_codes(self)
+
+        check_return_code(self, failed_services, expected_code)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/product_add_filter_test b/product_add_filter_test
new file mode 100644
index 0000000..620789e
--- /dev/null
+++ b/product_add_filter_test
@@ -0,0 +1,160 @@
+MODULE NAME
+    product_add_filter_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_missing_filter_generates_error(self)
+
+        test_no_error_if_org_and_product_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class AddProductFilterTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_returns_with_error_if_no_product_was_found(self)
+
+        test_it_uses_filter_api_to_retrieve_filter_info(self)
+
+        test_it_returns_with_error_if_filter_was_not_found(self)
+
+        test_it_retrieves_all_product_filters(self)
+
+        test_it_calls_update_filter_api(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            PROD = test_data.PRODUCTS [0]
+
+            FILTER = 'filter'
+
+            FILTER1 = 'filter_1'
+
+            FILTER2 = 'filter_2'
+
+            EXISTING_FILTERS = [{'name': FILTER1}, {'name': FILTER2}]
+
+            OPTIONS = {'org': ORG ['name'], 'name': PROD ['name'], 'filt...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/product_cancel_sync_test b/product_cancel_sync_test
new file mode 100644
index 0000000..dd839f2
--- /dev/null
+++ b/product_cancel_sync_test
@@ -0,0 +1,87 @@
+MODULE NAME
+    product_cancel_sync_test
+
+
+CLASSES
+
+    class ProductStatusTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_finds_the_product(self)
+
+        test_it_returns_with_error_when_no_product_found(self)
+
+        test_it_calls_cancel_sync_api(self)
+
+        test_it_returns_ok(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            PROV = test_data.PROVIDERS [2]
+
+            PROD = test_data.PRODUCTS [0]
+
+            OPTIONS = {'org': ORG ['name'], 'name': PROD ['name']}
+
+            product = None
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/product_create_test b/product_create_test
new file mode 100644
index 0000000..68fadf3
--- /dev/null
+++ b/product_create_test
@@ -0,0 +1,156 @@
+MODULE NAME
+    product_create_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_missing_prov_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class CreateTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_finds_provider(self)
+
+        test_creates_product(self)
+
+        test_discovers_repos(self)
+
+        test_creates_product_without_repositories_if_url_was_not_specified(self)
+
+        test_selects_repos(self)
+
+        test_create_repos(self)
+
+        Class variables
+
+            PROVIDER = 'provider'
+
+            PROVIDER_ID = '123'
+
+            ORGANIZATION = 'org'
+
+            PRODUCT = 'product1'
+
+            PRODUCT_ID = '123'
+
+            DESCRIPTION = 'description'
+
+            URL = 'http://localhost'
+
+            DISCOVERED_REPOS = ['url1', 'url2']
+
+            ASSUMEYES = True
+
+            NODISC = False
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/product_delete_filter_test b/product_delete_filter_test
new file mode 100644
index 0000000..760d79d
--- /dev/null
+++ b/product_delete_filter_test
@@ -0,0 +1,158 @@
+MODULE NAME
+    product_delete_filter_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_missing_filter_generates_error(self)
+
+        test_no_error_if_org_and_product_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class DeleteProductFilterTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_returns_with_error_if_no_product_was_found(self)
+
+        test_it_uses_filter_api_to_retrieve_filter_info(self)
+
+        test_it_returns_with_error_if_filter_was_not_found(self)
+
+        test_it_retrieves_all_product_filters(self)
+
+        test_it_calls_update_filter_api(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            PROD = test_data.PRODUCTS [0]
+
+            FILTER1 = 'filter_1'
+
+            FILTER2 = 'filter_2'
+
+            EXISTING_FILTERS = [{'name': FILTER1}, {'name': FILTER2}]
+
+            OPTIONS = {'org': ORG ['name'], 'name': PROD ['name'], 'filt...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/product_delete_test b/product_delete_test
new file mode 100644
index 0000000..c9d03ff
--- /dev/null
+++ b/product_delete_test
@@ -0,0 +1,83 @@
+MODULE NAME
+    product_delete_test
+
+
+CLASSES
+
+    class DeleteTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_finds_the_product(self)
+
+        test_it_returns_error_when_product_not_found(self)
+
+        test_it_calls_delete_api(self)
+
+        test_it_returns_status_ok(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            PROD = test_data.PRODUCTS [0]
+
+            OPTIONS = {'org': ORG ['name'], 'name': PROD ['name']}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/product_list_filter_test b/product_list_filter_test
new file mode 100644
index 0000000..98df3cc
--- /dev/null
+++ b/product_list_filter_test
@@ -0,0 +1,82 @@
+MODULE NAME
+    product_list_filter_test
+
+
+CLASSES
+
+    class ProductListFiltersTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_returns_with_error_if_no_product_was_found(self)
+
+        test_it_uses_product_list_filter_api(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            PROD = test_data.PRODUCTS [0]
+
+            OPTIONS = {'org': ORG ['name'], 'name': PROD ['name']}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/product_list_test b/product_list_test
new file mode 100644
index 0000000..7b55837
--- /dev/null
+++ b/product_list_test
@@ -0,0 +1,159 @@
+MODULE NAME
+    product_list_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_no_error_if_org_provided(self)
+
+        test_no_error_if_org_and_env_provided(self)
+
+        test_no_error_if_org_and_provider_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class ProductListTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_finds_environment(self)
+
+        test_it_finds_products_by_environment(self)
+
+        test_it_finds_provider(self)
+
+        test_it_finds_products_by_provider(self)
+
+        test_it_prints_products(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            ENV = test_data.ENVS [0]
+
+            PROV = test_data.PROVIDERS [2]
+
+            OPTIONS_BY_ENV = {'org': ORG ['name'], 'env': ENV ['name']}
+
+            OPTIONS_BY_PROVIDER = {'org': ORG ['name'], 'prov': PROV ['n...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/product_promote_test b/product_promote_test
new file mode 100644
index 0000000..dacf517
--- /dev/null
+++ b/product_promote_test
@@ -0,0 +1,163 @@
+MODULE NAME
+    product_promote_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_environment_generates_error(self)
+
+        test_no_error_if_org_and_product_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class ProductPromoteTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_finds_the_environment(self)
+
+        test_it_returns_with_error_when_no_environment_found(self)
+
+        test_it_creates_new_changeset(self)
+
+        test_it_updates_the_changeset(self)
+
+        test_it_promotes_the_changeset(self)
+
+        test_waits_for_promotion(self)
+
+        test_it_deletes_the_changeset(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            ENV = test_data.ENVS [0]
+
+            PROV = test_data.PROVIDERS [2]
+
+            PROD = test_data.PRODUCTS [0]
+
+            CSET = test_data.EMPTY_CHANGESET
+
+            TMP_CHANGESET_NAME = 'tmp_changeset_name'
+
+            OPTIONS = {'org': ORG ['name'], 'name': PROD ['name'], 'env'...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/product_status_test b/product_status_test
new file mode 100644
index 0000000..e47d7b8
--- /dev/null
+++ b/product_status_test
@@ -0,0 +1,92 @@
+MODULE NAME
+    product_status_test
+
+
+CLASSES
+
+    class ProductStatusTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_finds_the_product(self)
+
+        test_it_returns_with_error_when_no_product_found(self)
+
+        test_it_calls_last_sync_status_api(self)
+
+        test_it_does_not_set_progress_for_not_running_sync(self)
+
+        test_it_sets_progress_for_running_sync(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            PROV = test_data.PROVIDERS [2]
+
+            PROD = test_data.PRODUCTS [0]
+
+            OPTIONS = {'org': ORG ['name'], 'name': PROD ['name']}
+
+            product = None
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/product_sync_test b/product_sync_test
new file mode 100644
index 0000000..cc05415
--- /dev/null
+++ b/product_sync_test
@@ -0,0 +1,91 @@
+MODULE NAME
+    product_sync_test
+
+
+CLASSES
+
+    class ProductSyncTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_finds_the_product(self)
+
+        test_returns_with_error_when_no_product_found(self)
+
+        test_calls_sync_api(self)
+
+        test_waits_for_sync(self)
+
+        test_returns_ok_when_sync_was_successful(self)
+
+        test_returns_error_if_sync_failed(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            ENV = test_data.ENVS [0]
+
+            PROV = test_data.PROVIDERS [2]
+
+            PROD = test_data.PRODUCTS [0]
+
+            OPTIONS = {'org': ORG ['name'], 'name': PROD ['name']}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/provider_cancel_sync_test b/provider_cancel_sync_test
new file mode 100644
index 0000000..8e32b44
--- /dev/null
+++ b/provider_cancel_sync_test
@@ -0,0 +1,85 @@
+MODULE NAME
+    provider_cancel_sync_test
+
+
+CLASSES
+
+    class ProviderCancelSyncTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_finds_the_provider(self)
+
+        test_it_returns_with_error_when_no_provider_found(self)
+
+        test_it_calls_cancel_sync_api(self)
+
+        test_returns_ok(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            PROV = test_data.PROVIDERS [2]
+
+            OPTIONS = {'org': ORG ['name'], 'name': PROV ['name']}
+
+            provider = None
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/provider_status_test b/provider_status_test
new file mode 100644
index 0000000..071064a
--- /dev/null
+++ b/provider_status_test
@@ -0,0 +1,90 @@
+MODULE NAME
+    provider_status_test
+
+
+CLASSES
+
+    class ProviderStatusTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_finds_the_provider(self)
+
+        test_it_returns_with_error_when_no_provider_found(self)
+
+        test_it_calls_last_sync_status_api(self)
+
+        test_it_does_not_set_progress_for_not_running_sync(self)
+
+        test_it_sets_progress_for_running_sync(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            PROV = test_data.PROVIDERS [2]
+
+            OPTIONS = {'org': ORG ['name'], 'name': PROV ['name']}
+
+            provider = None
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/provider_sync_test b/provider_sync_test
new file mode 100644
index 0000000..6cb0aef
--- /dev/null
+++ b/provider_sync_test
@@ -0,0 +1,79 @@
+MODULE NAME
+    provider_sync_test
+
+
+CLASSES
+
+    class SyncTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_finds_provider(self)
+
+        test_returns_with_error_when_no_provider_found(self)
+
+        test_calls_sync_api(self)
+
+        test_waits_for_sync(self)
+
+        test_returns_ok_when_sync_was_successful(self)
+
+        test_returns_error_if_sync_failed(self)
+
+        Class variables
+
+            PROVIDER = 'provider'
+
+            PROVIDER_ID = '123'
+
+            ORGANIZATION = 'org'
+
+            SYNC_RESULT_WITHOUT_ERROR = test_data.SYNC_RESULT_WITHOUT_ERROR
+
+            SYNC_RESULT_WITH_ERROR = test_data.SYNC_RESULT_WITH_ERROR
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/repo_create_test b/repo_create_test
new file mode 100644
index 0000000..2653371
--- /dev/null
+++ b/repo_create_test
@@ -0,0 +1,340 @@
+MODULE NAME
+    repo_create_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_missing_url_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class RepoDiscoveryTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_performs_pulp_repo_discovery(self)
+
+        test_polls_pulp(self)
+
+        test_exit_when_no_repos_were_discovered(self)
+
+        Class variables
+
+            RESULT = {'result': []}
+
+            DISCOVERY_TASK = {}
+
+            URL = 'http://localhost'
+
+            ORG = 'ACME'
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class RepositorySelectionTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_q_forces_exit(self)
+
+        test_a_y_adds_all_discovered_repos(self)
+
+        test_1_y_adds_first_discovered_repo(self)
+
+        test_assumeyes_adds_all_discovered_repos(self)
+
+        Class variables
+
+            DISCOVERED_URLS = ['http://localhost/a/b/', 'http://localhos...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class RepositoryNameTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_replaces_slashes_with_underscores(self)
+
+        Class variables
+
+            NAME = 'REPO'
+
+            URL = 'http://localhost/a/b/'
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+            unittest.TestCase.tearDown(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class CreateRepositoryTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_create_repo_in_pulp(self)
+
+        test_creates_repos_in_pulp_for_all_urls(self)
+
+        Class variables
+
+            PRODUCT_ID = '123'
+
+            NAME = 'REPO'
+
+            URL = 'http://localhost/a/b/'
+
+            URL2 = 'http://localhost/a/c/'
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+            unittest.TestCase.tearDown(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class RawInputStub
+
+
+        __init__(self, input)
+
+        raw_input(self, prompt)
diff --git a/repo_delete_test b/repo_delete_test
new file mode 100644
index 0000000..990d889
--- /dev/null
+++ b/repo_delete_test
@@ -0,0 +1,162 @@
+MODULE NAME
+    repo_delete_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_missing_repo_name_generates_error(self)
+
+        test_missing_repo_id_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        test_no_error_if_required_repo_id_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class DeleteTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_finds_repo_by_id(self)
+
+        test_finds_repo_by_name(self)
+
+        test_returns_with_error_when_no_repo_found(self)
+
+        test_it_calls_delete_api(self)
+
+        test_it_returns_status_ok(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            PROD = test_data.PRODUCTS [0]
+
+            REPO = test_data.REPOS [0]
+
+            ENV = test_data.ENVS [0]
+
+            OPTIONS_WITH_ID = {'id': REPO ['id'],}
+
+            OPTIONS_WITH_NAME = {'name': REPO ['name'], 'product': PROD ...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/repo_list_test b/repo_list_test
new file mode 100644
index 0000000..131e6b6
--- /dev/null
+++ b/repo_list_test
@@ -0,0 +1,166 @@
+MODULE NAME
+    repo_list_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_no_error_if_org_provided(self)
+
+        test_no_error_if_org_and_env_provided(self)
+
+        test_no_error_if_org_and_product_provided(self)
+
+        test_no_error_if_org_product_and_env_provided(self)
+
+        test_no_error_if_org_product_and_disabled_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class RepoListTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_finds_environment(self)
+
+        test_it_finds_product(self)
+
+        test_it_finds_product_and_env(self)
+
+        test_it_gets_repos_by_org(self)
+
+        test_it_gets_repos_by_product(self)
+
+        test_it_gets_repos_by_product_and_env(self)
+
+        test_it_prints_repos(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            ENV = test_data.ENVS [0]
+
+            PROD = test_data.PRODUCTS [0]
+
+            OPTIONS_BY_ORG = {'org': ORG ['name'], 'env': ENV ['name']}
+
+            OPTIONS_BY_PRODUCT = {'org': ORG ['name'], 'product': PROD [...
+
+            OPTIONS_BY_PRODUCT_ENV = {'org': ORG ['name'], 'product': PR...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/repo_status_test b/repo_status_test
new file mode 100644
index 0000000..7511239
--- /dev/null
+++ b/repo_status_test
@@ -0,0 +1,169 @@
+MODULE NAME
+    repo_status_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_missing_repo_name_generates_error(self)
+
+        test_missing_repo_id_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        test_no_error_if_required_repo_id_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class RepoStatusTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_finds_repo_by_id(self)
+
+        test_finds_repo_by_name(self)
+
+        test_returns_with_error_when_no_repo_found(self)
+
+        test_it_calls_last_sync_status_api(self)
+
+        test_it_does_not_set_progress_for_not_running_sync(self)
+
+        test_it_sets_progress_for_running_sync(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            PROD = test_data.PRODUCTS [0]
+
+            REPO = test_data.REPOS [0]
+
+            ENV = test_data.ENVS [0]
+
+            OPTIONS_WITH_ID = {'id': REPO ['id'],}
+
+            OPTIONS_WITH_NAME = {'name': REPO ['name'], 'product': PROD ...
+
+            repo = None
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/repo_sync_test b/repo_sync_test
new file mode 100644
index 0000000..be218fc
--- /dev/null
+++ b/repo_sync_test
@@ -0,0 +1,171 @@
+MODULE NAME
+    repo_sync_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_missing_repo_name_generates_error(self)
+
+        test_missing_repo_id_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        test_no_error_if_required_repo_id_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class SynchronizeTestWithRepoId(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_finds_repo_by_id(self)
+
+        test_finds_repo_by_name(self)
+
+        test_returns_with_error_when_no_repo_found(self)
+
+        test_calls_sync_api(self)
+
+        test_waits_for_sync(self)
+
+        test_returns_ok_when_sync_was_successful(self)
+
+        test_returns_error_if_sync_failed(self)
+
+        Class variables
+
+            REPO_ID = 'repo_123'
+
+            REPO_NAME = 'repo_name'
+
+            REPO = {'id': REPO_ID, 'name': REPO_NAME}
+
+            OPTIONS_WITH_ID = {'id': REPO_ID,}
+
+            OPTIONS_WITH_NAME = {'name': REPO_NAME, 'product': 'product_...
+
+            SYNC_RESULT_WITHOUT_ERROR = [{'state': 'finished'}]
+
+            SYNC_RESULT_WITH_ERROR = [{'state': 'error', 'result': json....
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/setup b/setup
new file mode 100644
index 0000000..2efbdaa
--- /dev/null
+++ b/setup
@@ -0,0 +1,13 @@
+MODULE NAME
+    setup
+
+
+FUNCTIONS
+
+    data_files()
+
+VARIABLES
+
+    packages = ["katello", "katello.client", "katello.client.api", "kate...
+
+    requires = "kerberos", "M2Crypto", "iniparse", "simplejson", "dateutil"
diff --git a/single_product_test b/single_product_test
new file mode 100644
index 0000000..3de99a7
--- /dev/null
+++ b/single_product_test
@@ -0,0 +1,70 @@
+MODULE NAME
+    single_product_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_no_error_if_org_and_product_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/single_provider_test b/single_provider_test
new file mode 100644
index 0000000..9181141
--- /dev/null
+++ b/single_provider_test
@@ -0,0 +1,70 @@
+MODULE NAME
+    single_provider_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/sync_provider_test b/sync_provider_test
new file mode 100644
index 0000000..b99fb88
--- /dev/null
+++ b/sync_provider_test
@@ -0,0 +1,146 @@
+MODULE NAME
+    sync_provider_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class SyncTest(unittest.TestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_finds_provider(self)
+
+        test_returns_with_error_when_no_provider_found(self)
+
+        test_calls_sync_api(self)
+
+        test_waits_for_sync(self)
+
+        test_returns_ok_when_sync_was_successful(self)
+
+        test_returns_error_if_sync_failed(self)
+
+        Class variables
+
+            PROVIDER = 'provider'
+
+            PROVIDER_ID = '123'
+
+            ORGANIZATION = 'org'
+
+            SYNC_TASK = {}
+
+            SYNC_RESULT_WITHOUT_ERROR = [{'state': 'finished'}, {'state'...
+
+            SYNC_RESULT_WITH_ERROR = [{'state': 'error', 'result': json....
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/sync_repo_test b/sync_repo_test
new file mode 100644
index 0000000..13fa8db
--- /dev/null
+++ b/sync_repo_test
@@ -0,0 +1,170 @@
+MODULE NAME
+    sync_repo_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_product_generates_error(self)
+
+        test_missing_repo_name_generates_error(self)
+
+        test_missing_repo_id_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        test_no_error_if_required_repo_id_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class SynchronizeTestWithRepoId(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_finds_repo_by_id(self)
+
+        test_finds_repo_by_name(self)
+
+        test_returns_with_error_when_no_repo_found(self)
+
+        test_calls_sync_api(self)
+
+        test_waits_for_sync(self)
+
+        test_returns_ok_when_sync_was_successful(self)
+
+        test_returns_error_if_sync_failed(self)
+
+        Class variables
+
+            REPO_ID = 'repo_123'
+
+            REPO_NAME = 'repo_name'
+
+            REPO = {'id': REPO_ID, 'name': REPO_NAME}
+
+            OPTIONS_WITH_ID = {'repo_id': REPO_ID,}
+
+            OPTIONS_WITH_NAME = {'name': REPO_NAME, 'product': 'product_...
+
+            SYNC_RESULT_WITHOUT_ERROR = [{'state': 'finished'}]
+
+            SYNC_RESULT_WITH_ERROR = [{'state': 'error', 'result': json....
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/system_report_test b/system_report_test
new file mode 100644
index 0000000..3df8fb7
--- /dev/null
+++ b/system_report_test
@@ -0,0 +1,149 @@
+MODULE NAME
+    system_report_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_no_error_if_org_provided(self)
+
+        test_no_error_if_org_and_env_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class UserReportTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_calls_report_api_with_default_format(self)
+
+        test_it_uses_format_parameter(self)
+
+        test_it_saves_pdf_report(self)
+
+        test_it_calls_report_by_env_api(self)
+
+        Class variables
+
+            ORG_ID = 'some_org'
+
+            ENV_NAME = 'env'
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/template_create_test b/template_create_test
new file mode 100644
index 0000000..3f277ab
--- /dev/null
+++ b/template_create_test
@@ -0,0 +1,164 @@
+MODULE NAME
+    template_create_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_org_and_name_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class TemplateCreateTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_finds_locker_environment(self)
+
+        test_it_returns_error_when_locker_not_found(self)
+
+        test_it_finds_parent_template(self)
+
+        test_it_calls_create_api(self)
+
+        test_it_calls_create_api_with_parent_id(self)
+
+        test_it_returns_error_when_creation_failed(self)
+
+        test_it_success_on_successful_creation(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            TPL = test_data.TEMPLATES [0]
+
+            LOCKER = test_data.LOCKER
+
+            TPL_DESC = "description of the template"
+
+            TPL_PARENT_NAME = 'parent_template'
+
+            TPL_PARENT_ID = 83
+
+            OPTIONS = {'org': ORG ['name'], 'name': TPL ['name'], 'descr...
+
+            OPTIONS_WITH_PARENT = {'org': ORG ['name'], 'name': TPL ['na...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/template_delete_test b/template_delete_test
new file mode 100644
index 0000000..dd325bf
--- /dev/null
+++ b/template_delete_test
@@ -0,0 +1,152 @@
+MODULE NAME
+    template_delete_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_org_and_name_provided(self)
+
+        test_no_error_if_org_name_and_env_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class TemplateInfoTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_finds_the_template(self)
+
+        test_it_returns_error_when_template_not_found(self)
+
+        test_it_returns_success_when_template_found(self)
+
+        test_it_calls_delete_api(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            ENV = test_data.ENVS [0]
+
+            TPL = test_data.TEMPLATES [0]
+
+            OPTIONS = {'org': ORG ['name'], 'env': ENV ['name'], 'name':...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/template_import_test b/template_import_test
new file mode 100644
index 0000000..d1f584d
--- /dev/null
+++ b/template_import_test
@@ -0,0 +1,156 @@
+MODULE NAME
+    template_import_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_file_generates_error(self)
+
+        test_no_error_if_org_and_file_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class TemplateImportTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_opens_the_file(self)
+
+        test_it_returns_error_on_file_exception(self)
+
+        test_it_finds_locker(self)
+
+        test_it_calls_template_import_api(self)
+
+        test_it_closes_the_file(self)
+
+        test_it_returns_status_ok(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            LOCKER = test_data.ENVS [0]
+
+            TPL = test_data.TEMPLATES [0]
+
+            OPTIONS = {'org': ORG ['name'], 'file': "/a/b/c/template.imp...
+
+            open_file = None
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/template_info_test b/template_info_test
new file mode 100644
index 0000000..eb73aac
--- /dev/null
+++ b/template_info_test
@@ -0,0 +1,152 @@
+MODULE NAME
+    template_info_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_org_and_name_provided(self)
+
+        test_no_error_if_org_name_and_env_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class TemplateInfoTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_finds_the_template(self)
+
+        test_it_returns_error_when_template_not_found(self)
+
+        test_it_returns_success_when_template_found(self)
+
+        test_it_prints_the_templates(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            ENV = test_data.ENVS [0]
+
+            TPL = test_data.TEMPLATES [0]
+
+            OPTIONS = {'org': ORG ['name'], 'env': ENV ['name'], 'name':...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/template_list_test b/template_list_test
new file mode 100644
index 0000000..cbfc24a
--- /dev/null
+++ b/template_list_test
@@ -0,0 +1,148 @@
+MODULE NAME
+    template_list_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_no_error_if_org_provided(self)
+
+        test_no_error_if_org_and_env_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class TemplateListTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_finds_environment(self)
+
+        test_it_returns_error_when_environment_not_found(self)
+
+        test_it_calls_templates_api(self)
+
+        test_it_prints_the_templates(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            ENV = test_data.ENVS [0]
+
+            OPTIONS = {'org': ORG ['name'], 'env': ENV ['name'],}
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/template_update_test b/template_update_test
new file mode 100644
index 0000000..46cebc5
--- /dev/null
+++ b/template_update_test
@@ -0,0 +1,164 @@
+MODULE NAME
+    template_update_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_org_generates_error(self)
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_org_and_name_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class TemplateUpdateTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_it_finds_the_template_by_name(self)
+
+        test_it_returns_error_when_template_not_found(self)
+
+        test_it_finds_parent_template(self)
+
+        test_it_returns_error_when_parent_not_found(self)
+
+        test_it_calls_update_template(self)
+
+        test_it_calls_update_content(self)
+
+        test_it_returns_status_ok(self)
+
+        Class variables
+
+            ORG = test_data.ORGS [0]
+
+            ENV = test_data.LOCKER
+
+            TPL = test_data.TEMPLATES [0]
+
+            TPL_DESC = "description of the template"
+
+            TPL_PARENT_NAME = 'parent_template'
+
+            TPL_PARENT_ID = 83
+
+            OPTIONS = {'org': ORG ['name'], 'name': TPL ['name']}
+
+            OPTIONS_WITH_PARENT = {'org': ORG ['name'], 'name': TPL ['na...
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/test_data b/test_data
new file mode 100644
index 0000000..41dea5a
--- /dev/null
+++ b/test_data
@@ -0,0 +1,47 @@
+MODULE NAME
+    test_data
+
+
+VARIABLES
+
+    ORGS = [{"name": "ACME_Corporation", "created_at": "2011-08-23T08:10...
+
+    ENVS = [{"name": "Locker", "prior": None, "created_at": "2011-08-23T...
+
+    LOCKER = ENVS [0]
+
+    PROVIDERS = [{"name": "porkchop", "created_at": "2011-08-29T08:19:02...
+
+    SLA_VALUE = "1"
+
+    PRODUCTS = [{"last_sync": None, "name": "prod_a1", "created_at": "20...
+
+    COMMON_ASYNC_RESULT_SUCCESS = [{"result": "{\"errors\":[null,null]}"...
+
+    SYNC_RESULT_NOT_SYNCED = [{"result": None, "created_at": None, "uuid...
+
+    SYNC_RESULT_WITHOUT_ERROR = [{"result": "{\"errors\":[null,null]}", ...
+
+    SYNC_RESULT_WITH_ERROR = [{"result": "{\"errors\":[\"some error 1\",...
+
+    SYNC_RESULT_CANCELLED = [{"result": "{\"errors\":[null,null]}", "cre...
+
+    SYNC_RUNNING_RESULT = [{"result": "{\"errors\":[null,null]}", "creat...
+
+    EMPTY_CHANGESET = {"name": "CS", "products": [], "created_at": "2011...
+
+    CHANGESETS = [EMPTY_CHANGESET]
+
+    REPOS = [{"package_count": 0, "name": "prod_a2_fakerepos_zoo", "clon...
+
+    ERRATA_BY_REPO = [{"title": "Zoo package enhancements", "_id": "RHEA...
+
+    PACKAGE_GROUPS = [{"name": "katello", "conditional_package_names": {...
+
+    PACKAGE_GROUP_CATEGORIES = [{"name": "Development", "_id": "developm...
+
+    TEMPLATES = [{"name": "tpl_a1", "products": [{"productContent": [], ...
+
+    POOL = {"href": "/pools/40288ae9333fe87201334033dd21001b", "sourceEn...
+
+    PING_STATUS = {'result': 'failed', 'status': {'candlepin': {'duratio...
diff --git a/uebercert_generate_test b/uebercert_generate_test
new file mode 100644
index 0000000..d060ce0
--- /dev/null
+++ b/uebercert_generate_test
@@ -0,0 +1,139 @@
+MODULE NAME
+    uebercert_generate_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class CreateUebercertTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_generates_uebercert_in_cp(self)
+
+        Class variables
+
+            NAME = 'ORG'
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/uebercert_info_test b/uebercert_info_test
new file mode 100644
index 0000000..f27a732
--- /dev/null
+++ b/uebercert_info_test
@@ -0,0 +1,139 @@
+MODULE NAME
+    uebercert_info_test
+
+
+CLASSES
+
+    class RequiredCLIOptionsTests(cli_test_utils.CLIOptionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        test_missing_name_generates_error(self)
+
+        test_no_error_if_required_options_provided(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIOptionTestCase.mock_options(self)
+            cli_test_utils.CLIOptionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+            cli_test_utils.CLITestCase.tearDown(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class CreateUebercertTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_generates_uebercert_in_cp(self)
+
+        Class variables
+
+            NAME = 'ORG'
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/usage b/usage
new file mode 100644
index 0000000..375ff7a
--- /dev/null
+++ b/usage
@@ -0,0 +1,20 @@
+MODULE NAME
+    usage
+
+
+CLASSES
+
+    class UsageGenerator
+
+
+        __init__(self)
+
+        collector(self)
+
+        _process_subcommands_for(self, cmd, parent_name)
+
+        print_usage(self, command="katello")
+
+        Class methods
+
+            _print_subcommands_for(cls, cmd)
diff --git a/user_report_test b/user_report_test
new file mode 100644
index 0000000..3dd72f6
--- /dev/null
+++ b/user_report_test
@@ -0,0 +1,76 @@
+MODULE NAME
+    user_report_test
+
+
+CLASSES
+
+    class UserReportTest(cli_test_utils.CLIActionTestCase)
+
+
+        setUp(self)
+            Hook method for setting up the test fixture before exercising
+            it.
+
+        tearDown(self)
+            Hook method for deconstructing the test fixture after testing
+            it.
+
+        test_it_calls_report_api_with_default_format(self)
+
+        test_it_uses_format_parameter(self)
+
+        test_it_saves_pdf_report(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            cli_test_utils.CLITestCase.mock(self, obj, property_name, return_value=None)
+            cli_test_utils.CLITestCase.mock_from_module(self, property_name, return_value=None)
+            cli_test_utils.CLIActionTestCase.mock_options(self, options)
+            cli_test_utils.CLIActionTestCase.mock_printer(self)
+            cli_test_utils.CLIActionTestCase.mock_spinner(self)
+            cli_test_utils.CLIActionTestCase.mocked_get_option(self, opt, default=None)
+            cli_test_utils.CLITestCase.restore_mocks(self)
+            unittest.TestCase.run(self, result=None)
+            cli_test_utils.CLITestCase.set_action(self, action)
+            cli_test_utils.CLITestCase.set_module(self, module)
+            unittest.TestCase.shortDescription(self)
+
+        Inherited class variables
+            cli_test_utils.CLITestCase._mocked_props = {}
+            cli_test_utils.CLIActionTestCase._options = {}
+            cli_test_utils.CLITestCase.action = None
+            cli_test_utils.CLITestCase.module = None
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
diff --git a/utils_test b/utils_test
new file mode 100644
index 0000000..aec5785
--- /dev/null
+++ b/utils_test
@@ -0,0 +1,117 @@
+MODULE NAME
+    utils_test
+
+
+CLASSES
+
+    class ConvertToMimeTest(unittest.TestCase)
+
+
+        test_text_conversion(self)
+
+        test_html_conversion(self)
+
+        test_csv_conversion(self)
+
+        test_pdf_conversion(self)
+
+        test_default_type(self)
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.setUp(self)
+            unittest.TestCase.shortDescription(self)
+            unittest.TestCase.tearDown(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)
+
+    class AttachmentFilenameTest(unittest.TestCase)
+
+
+        test_uses_filename_in_content_disposition_header(self)
+
+        test_handles_capitalized_header_name(self)
+
+        test_uses_default_filename_without_content_disposition_header(self)
+
+        test_uses_default_filename_with_incomplete_content_disposition_header(self)
+
+        Class variables
+
+            FILENAME = 'test_file.txt'
+
+            DEFAULT_FILENAME = 'default.txt'
+
+        Inherited methods
+            unittest.TestCase.__call__(self, *args, **kwds)
+            unittest.TestCase.__eq__(self, other)
+            unittest.TestCase.__hash__(self)
+            unittest.TestCase.__init__(self, methodName='runTest')
+            unittest.TestCase.__ne__(self, other)
+            unittest.TestCase.__repr__(self)
+            unittest.TestCase.__str__(self)
+            unittest.TestCase._exc_info(self)
+            unittest.TestCase.assertAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertEqual(self, first, second, msg=None)
+            unittest.TestCase.assertEquals(self, first, second, msg=None)
+            unittest.TestCase.assertFalse(self, expr, msg=None)
+            unittest.TestCase.assertNotAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotAlmostEquals(self, first, second, places=7, msg=None)
+            unittest.TestCase.assertNotEqual(self, first, second, msg=None)
+            unittest.TestCase.assertNotEquals(self, first, second, msg=None)
+            unittest.TestCase.assertRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.assertTrue(self, expr, msg=None)
+            unittest.TestCase.assert_(self, expr, msg=None)
+            unittest.TestCase.countTestCases(self)
+            unittest.TestCase.debug(self)
+            unittest.TestCase.defaultTestResult(self)
+            unittest.TestCase.fail(self, msg=None)
+            unittest.TestCase.failIf(self, expr, msg=None)
+            unittest.TestCase.failIfAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failIfEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnless(self, expr, msg=None)
+            unittest.TestCase.failUnlessAlmostEqual(self, first, second, places=7, msg=None)
+            unittest.TestCase.failUnlessEqual(self, first, second, msg=None)
+            unittest.TestCase.failUnlessRaises(self, excClass, callableObj, *args, **kwargs)
+            unittest.TestCase.id(self)
+            unittest.TestCase.run(self, result=None)
+            unittest.TestCase.setUp(self)
+            unittest.TestCase.shortDescription(self)
+            unittest.TestCase.tearDown(self)
+
+        Inherited nested classes
+            class unittest.TestCase.failureException(StandardError)


```

readme
=======================

```diff

diff --git a/README b/README
new file mode 100644
index 0000000..139597f
--- /dev/null
+++ b/README
@@ -0,0 +1,2 @@
+
+
