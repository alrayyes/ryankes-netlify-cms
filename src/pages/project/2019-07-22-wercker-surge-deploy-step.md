---
templateKey: blog-post
title: ' Wercker Surge Deploy Step'
date: 2016-10-07T18:32:00.000Z
featuredimage: /img/wercker-surge-deploy-step_canvas.jpg
tags:
  - wercker step surge surge.sh
---
[![wercker status](https://app.wercker.com/status/53608930d55146d82ac67f64a6b82e74/m "wercker status")](https://app.wercker.com/project/bykey/53608930d55146d82ac67f64a6b82e74)​

In order to make my life a little bit easier I've created a [Wercker step](https://app.wercker.com/applications/57f405b206790b01005546d4/tab/details/) to deploy to [Surge](https://surge.sh/). Here's the main code as of the time of writing (version 0.2.3)

## wercker-step.yml

```yaml
name: surge-deploy
version: 0.2.3
description: Deploys a directory to surge.sh.
keywords:
  - deploy
  - surge.sh
properties:
  login:
    type: string
    required: true
  directory:
    type: string
    default: public
    required: true
  token:
    type: string
    required: true
  domain:
    type: string
    required: true
```

## run.sh

```bash
#!/bin/sh

# return true if local npm package is installed at ./node_modules, else false
# example
# echo "surge : $(npm_package_is_installed surge)"
function npm_package_is_installed {
  # set to true initially
  local return_=true
  # set to false if not found
  ls node_modules | grep $1 >/dev/null 2>&1 || { local return_=false; }
  # return value
  echo "$return_"
}

# First make sure surge is installed
if ! type surge &> /dev/null ; then
    # Check if it is in repo
    if ! $(npm_package_is_installed surge) ; then
        info "surge not installed, trying to install it through npm"

        if ! type npm &> /dev/null ; then
            fail "npm not found, make sure you have npm or surge installed"
        else
            info "npm is available"
            debug "npm version: $(npm --version)"

            info "installing surge"
            npm config set ca "" --silent
            sudo npm install npm -g --silent
            sudo npm install -g --silent surge
            surge_command="surge"
        fi
    else
        info "surge is available locally"
        debug "surge version: $( node ./node_modules/.bin/surge --version)"
        surge_command=" node ./node_modules/.bin/surge"
    fi
else
    info "surge is available"
    debug "surge version: $(surge --version)"
    surge_command="surge"
fi

SURGE_LOGIN=${WERCKER_SURGE_DEPLOY_LOGIN}
surge_command="${surge_command} ${WERCKER_SURGE_DEPLOY_DIRECTORY} ${WERCKER_SURGE_DEPLOY_DOMAIN} --token ${WERCKER_SURGE_DEPLOY_TOKEN}"

debug "$surge_command"

set +e
$surge_command
result="$?"
set -e

# Fail if it is not a success or warning
if [[ result -ne 0 && result -ne 6 ]]
then
    warn "$result"
    fail "surge command failed"
else
    success "finished $surge_command"
fi
```

---

## Parameters

| Name | Required | Description | Default |
| --- | :---: | --- | --- |
| login | yes | The surge.sh login. The login can be echoed by running {{< highlight shell >}}surge token{{< /highlight >}} locally ||
| directory | no | The directory to de deployed | public |
| token | yes | The surge.sh token. The login can be echoed by running {{< highlight shell >}}surge token{{< /highlight >}} locally ||
| domain | yes | Domain to be used with surge.sh hosting ||

## Example wercker.yml (Docker)

``` yaml
box: andthensome/alpine-surge-bash
deploy:
  steps:
    - alrayyes/surge-deploy@0.2.3:
        login: "user@example.com"
        directory: "public"
        token: "94a08da1fecbb6e8b46990538c7b50b2"
        domain: "example.com"
```

---

It's all pretty straight forward. As this is my first Wercker step I'm sure there's plenty of room for improvement. Please feel free to [post an issue](https://github.com/alrayyes/wercker-surge-deploy-step/issues) or submit a [pull request](https://github.com/alrayyes/wercker-surge-deploy-step/pulls).
