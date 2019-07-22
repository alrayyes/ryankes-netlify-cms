---
templateKey: blog-post
title: How To Trigger Builds With the Wercker API
date: 2016-09-24T11:50:00.000Z
description: >
  This site is built using [Hugo](https://gohugo.io) with automatic builds &
  deployments handled by [Wercker](http://wercker.com). To keep the [tunes
  section]({{< ref "/tunes.md" >}}) of this site up to date builds are triggered
  using the [Wercker API](http://devcenter.wercker.com/api/). Unfortunately
  decent documentation is a bit lacking when it comes to clarity, so here's a
  little tutorial on how I got this to work. Important stuff in the api return
  json is highlighted.
featuredpost: false
featuredimage: /img/header.jpg
tags:
  - wercker tutorial
---
This site is built using [Hugo](https://gohugo.io) with automatic builds & deployments handled by [Wercker](http://wercker.com). To keep the [tunes section]({{< ref "/tunes.md" >}}) of this site up to date builds are triggered using the [Wercker API](http://devcenter.wercker.com/api/). Unfortunately decent documentation is a bit lacking when it comes to clarity, so here's a little tutorial on how I got this to work. Important stuff in the api return json is highlighted.

* * *

## Parameters we need

### `<token>`
According to the [documentation](http://devcenter.wercker.com/api/getting-started/authentication.html) this can be generated in the [profile settings](https://app.wercker.com/profile/tokens)

![Generate Wercker token](generate-token.png)

For sake of argument lets say that the generated token is *7c6a180b36896a0a8c02787eeafb0e4c*

### `<applicationId>`
To get the `<applicationId>` the [appropriate api call](http://devcenter.wercker.com/api/endpoints/applications.html#get-an-application) would be:

```bash
curl -H 'Content-Type: application/json' -H  'Authorization: Bearer <token>' https://app.wercker.com/api/v3/applications/<username>/<application>
```

So lets try this filling in my username (alrayyes) and application (ryankes.eu):

```bash
curl -H 'Content-Type: application/json' -H  'Authorization: Bearer 7c6a180b36896a0a8c02787eeafb0e4c' https://app.wercker.com/api/v3/applications/alrayyes/ryankes.eu
```

The response:
```json{2}
{
   "id":"b4bd7238111424a17180fc52c88fed96",
   "url":"https://app.wercker.com/api/v3/applications/alrayyes/ryankes.eu",
   "name":"ryankes.eu",
   "owner":{ 
      "type":"wercker",
      "name":"alrayyes",
      "avatar":{
         "gravatar":"0cc175b9c0f1b6a831c399e269772661"
      },
      "userId":"24779006937429bb5dbda1896edbeb98",
      "meta":{
         "username":"alrayyes",
         "type":"user",
         "werckerEmployee":false
      }
   },
   "builds":"https://app.wercker.com/api/v3/applications/alrayyes/ryankes.eu/builds",
   "deploys":"https://app.wercker.com/api/v3/applications/alrayyes/ryankes.eu/deploys",
   "scm":{
      "type":"git",
      "owner":"alrayyes",
      "domain":"bitbucket.org",
      "repository":"ryankes.eu"
   },
   "badgeKey":"f97c5d29941bfb1b2fdab0874906ab82",
   "createdAt":"2016-09-20T22:28:31.177Z",
   "updatedAt":"2016-09-20T22:28:31.177Z",
   "allowedActions":[
      "delete_project",
      "update_project",
      "transfer_ownership_project",
      "update_checkout_key",
      "list_authorizations",
      "create_authorization",
      "read_authorization",
      "update_authorization",
      "delete_authorization",
      "read_heroku_collaborators",
      "send_invites",
      "create_key",
      "delete_key",
      "update_key",
      "update_teams",
      "delete_team",
      "update_org",
      "create_member",
      "delete_member",
      "update_member",
      "create_team_members",
      "delete_team_members",
      "org_create_app",
      "org_transferownership_app",
      "org_transfer_apiUser_app",
      "update_project_button",
      "cancel_customer",
      "create_customer",
      "get_customer",
      "update_customer",
      "get_billing",
      "update_billing",
      "update_extended_permissions",
      "get_invoice",
      "list_invoices",
      "deploy_build",
      "reset_deploy_warnings",
      "create_deploytarget",
      "create_target",
      "update_target",
      "delete_target",
      "create_target_trigger",
      "update_target_trigger",
      "read_target_trigger",
      "delete_target_trigger",
      "read_deploytarget",
      "abort_deploy",
      "update_deploytarget",
      "delete_deploytarget",
      "read_deploytargetsettings",
      "application_envcollection",
      "list_key",
      "read_key",
      "create_env_var",
      "update_env_var",
      "delete_env_var",
      "build_project",
      "rebuild_build",
      "abort_build",
      "read_deploy",
      "list_deploysteps",
      "read_deploystep",
      "read_deploytarget_logs",
      "add_pullrequest",
      "receive_push",
      "autodeploy_build",
      "fix_webhook",
      "clear_cache_project",
      "list_teams",
      "list_members",
      "org_read_wallItem",
      "read_docker_image",
      "create_run",
      "abort_run",
      "list_extended_permissions",
      "read_env_var",
      "read_project",
      "list_builds",
      "read_build",
      "list_runs",
      "read_run",
      "list_runsteps",
      "read_runstep",
      "list_buildsteps",
      "read_buildstep",
      "follow_project",
      "unfollow_project",
      "list_targets",
      "read_target",
      "list_workflows",
      "list_collaborators",
      "list_comment",
      "create_comment"
   ],
   "theme":"Oranje",
   "settings":{
      "privacy":"private",
      "stack":6,
      "ignoredBranches":[

      ]
   }
}
```

So in this case the `<applicationId>` is *b4bd7238111424a17180fc52c88fed96*

### `<pipelineId>`

To get the `<pipelineId>` the [appropriate api call](http://devcenter.wercker.com/api/endpoints/workflows.html#get-all-workflows) would be:

```bash
curl  -H 'Content-Type: application/json' -H  'Authorization: Bearer <token>' https://app.wercker.com/api/v3/workflows?applicationId=<applicationId>
```

Lets run this with our application id:

```bash
curl  -H 'Content-Type: application/json' -H  'Authorization: Bearer 7c6a180b36896a0a8c02787eeafb0e4c' https://app.wercker.com/api/v3/workflows?applicationI=b4bd7238111424a17180fc52c88fed96
```

The response:
```json{21}
[
   {
      "url":"https://app.wercker.com/api/v3/workflows/7e786a17b9da91e2fe17a45fe63fb3e2",
      "createdAt":"2016-09-24T13:37:31.436Z",
      "data":{
         "branch":"master",
         "commitHash":"c6130827850af1c13ad1d19487232308",
         "message":"Merge branch 'hotfix/2.3.7'\n",
         "scm":{
            "repository":"ryankes.eu",
            "domain":"bitbucket.org",
            "owner":"alrayyes",
            "type":"git"
         }
      },
      "id":"7e786a17b9da91e2fe17a45fe63fb3e2",
      "items":[
         {
            "data":{
               "targetName":"build",
               "pipelineId":"01f995498924d4e3024b52e3941c8468",
               "restricted":false,
               "totalSteps":6,
               "currentStep":6,
               "stepName":"store",
               "runId":"33bab24842fa8e22446b5a233572933b"
            },
            "id":"fafe1b60c24107ccd8f4562213e44849",
            "progress":100,
            "result":"passed",
            "status":"finished",
            "type":"run",
            "updatedAt":"2016-09-24T13:37:58.868Z"
         },
         {
            "data":{
               "targetName":"deploy-production",
               "pipelineId":"6cae0f032acc7acbdf8ca52a9a6ed81b",
               "restricted":false,
               "totalSteps":5,
               "currentStep":5,
               "stepName":"store",
               "runId":"f4b003f5ca1419b0bbbee7e9b4ccf935"
            },
            "id":"d1ec3fe3aa3b0ebdb37f2bf7cddf27dc",
            "parentItem":"fafe1b60c24107ccd8f4562213e44849",
            "progress":100,
            "result":"passed",
            "status":"finished",
            "type":"run",
            "updatedAt":"2016-09-24T13:38:25.841Z"
         }
      ],
      "startedAt":"2016-09-24T13:37:33.696Z",
      "trigger":"git",
      "updatedAt":"2016-09-24T13:38:25.839Z",
      "user":{
         "meta":{
            "werckerEmployee":false,
            "type":"user",
            "username":"alrayyes"
         },
         "userId":"24779006937429bb5dbda1896edbeb98",
         "avatar":{
            "gravatar":"0cc175b9c0f1b6a831c399e269772661"
         },
         "name":"alrayyes",
         "type":"wercker"
      }
   }
]
```

Because we want to trigger the build itself we take the first pipeline id (*01f995498924d4e3024b52e3941c8468*)

* * *

## The run trigger itself

To recap, we now have all the data we need:

| `<token>` | `<pipelineId>` |
|-----------|-------------------|
|7c6a180b36896a0a8c02787eeafb0e4c|01f995498924d4e3024b52e3941c8468|

[Documentation](http://devcenter.wercker.com/api/endpoints/runs.html#trigger-a-run) says we need to do the following:

```bash
curl  -H 'Content-Type: application/json' -H  'Authorization: Bearer <token>' -X POST -d '{"pipelineId": "<pipelineId>"}' https://app.wercker.com/api/v3/runs/
```

The command with our data would be:

```bash
curl  -H 'Content-Type: application/json' -H  'Authorization: Bearer 7c6a180b36896a0a8c02787eeafb0e4c' -X POST -d '{"pipelineId": "01f995498924d4e3024b52e3941c8468"}' https://app.wercker.com/api/v3/runs/
```

And the final return code:

```json
{  
   "id":"dc264945402b29a2e0a176081ac94459",
   "url":"https://app.wercker.com/api/v3/runs/dc264945402b29a2e0a176081ac94459",
   "branch":"master",
   "commitHash":"cac9c20b6af734e6ce3dbe6f1d6e87f6",
   "createdAt":"2016-09-24T14:19:24.786Z",
   "envVars":[  

   ],
   "message":"Merge branch 'hotfix/2.3.7'\n",
   "commits":[  

   ],
   "progress":0,
   "result":"unknown",
   "status":"notstarted",
   "user":{  
      "meta":{  
         "username":"alrayyes",
         "type":"user",
         "werckerEmployee":false
      },
      "userId":"24779006937429bb5dbda1896edbeb98",
      "avatar":{  
         "gravatar":"0cc175b9c0f1b6a831c399e269772661"
      },
      "name":"alrayyes",
      "type":"wercker"
   },
   "pipeline":{  
      "id":"01f995498924d4e3024b52e3941c8468",
      "url":"https://app.wercker.com/api/v3/pipelines/01f995498924d4e3024b52e3941c8468",
      "createdAt":"2016-09-20T22:28:31.862Z",
      "name":"build",
      "permissions":"public",
      "pipelineName":"build",
      "setScmProviderStatus":true,
      "type":"git"
   }
}
```

![Succesfull Wercker build](build.png)

* * *

## Success!
