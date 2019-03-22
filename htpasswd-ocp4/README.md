# OAUth Config and HTPasswd for OCP4

The provided Ansible Playbook Bundle automates preparing an OpenShift cluster with an HTPasswd configuration and creating users.

## Use an installed APB on OpenShift
Select the item from the catalog.

## Development
When you develop the APB you can test on a platform in the following ways.

### Install the apb tool
The new `apb` tool will help you develop your APB in an easy way. Download the latest apb tool 
from here: [https://github.com/automationbroker/apb/releases](https://github.com/automationbroker/apb/releases)

The [README.md](https://github.com/automationbroker/apb/blob/master/README.md) in that repository will give you a short getting started very insightful.

If you're starrting a new APB, you should look into the workflow in the [Getting started doc](https://github.com/automationbroker/apb/blob/master/docs/getting_started.md)

### Create a namespace to test
Once logged into your OCP cluster, you must first create a project:

```bash
oc new-project ocp-workshops
```

### Grant service accounts in the test project the cluster-admin role

```bash
oc adm policy add-cluster-role-to-group sudoer system:serviceaccounts:ocp-workshops
```

### Update the com.redhat.apb.spec LABEL
If you've changed the apb.yml you will need to update the `com.redhat.apb.spec` LABEL with a base64 encoded version of apb.yml. To do this we need to run `apb bundle prepare`

```bash
apb bundle prepare
```

### Building and publishing your APB for testing

At this point we have a fully formed APB that we can build. In this example, we'll build an APB using the OpenShift build system. Let's first create a `buildconfig` for the APB. Perform this step when you're building a new APB that you haven't previously built on OpenShift:

```bash
oc new-build --binary=true --name htpasswd-ocp4
```

To start the build from the current directory and follow the logs:

```bash
oc start-build --follow --from-dir . htpasswd-ocp4
```

If the build completes successfully, you'll see some newly available imagestreams:

```bash
oc get is
NAME           DOCKER REPO                                  TAGS      UPDATED
apb-base       172.30.1.1:5000/ocp-workshops/apb-base       latest    4 minutes ago
htpasswd-ocp4  172.30.1.1:5000/ocp-workshops/htpasswd-ocp4  latest    3 seconds ago
```

We can now add the internal OpenShift registry to our list of configured registries:

```bash
apb registry add my-registry --type local_openshift --namespaces ocp-workshops
Getting specs for registry: [my-registry]
INFO == REGISTRY CX ==
INFO Name: my-registry
INFO Type: local_openshift
INFO Url:
INFO Validating specs...
WARN Spec [  ] failed validation for the following reason: [ Spec [] failed version validation ]. It will not be made available.
WARN 1 specs of 2 discovered specs failed validation from registry: openshift-registry
INFO Registry my-registry has 1 valid APBs available from 2 images scanned
  APB                   IMAGE                                               REGISTRY
  ----------------- -+- ------------------------------------------- -+- -----------
  htpasswd-ocp4  |  172.30.1.1:5000/ocp-workshops/htpasswd-ocp4  |  my-registry
```

__NOTE__: If you had the registry previously registered (maybe another cluster), you can delete it with: `apb registry remove my-registry`

### Running the APB
Now that `apb` is aware of the `htpasswd-ocp4` APB in the registry, we can perform any supported action against the APB. The `apb bundle` subcommand gives you some actions you can perform on a specific bundle in a registry. You can use the `--help` flag to see what options are available:

```
apb bundle --help
List, execute and build APBs

Usage:
  apb bundle [command]

Available Commands:
  deprovision Deprovision APB images
  info        Print info on APB image
  list        List APB images
  prepare     Stamp APB metadata onto Dockerfile as b64
  provision   Provision APB images
  test        Test APB images

Flags:
  -h, --help                help for bundle
  -k, --kubeconfig string   Path to kubeconfig to use

Global Flags:
      --config string   configuration file (default is $HOME/.apb)
  -v, --verbose         verbose output

Use "apb bundle [command] --help" for more information about a command.
```

We can `provision` the `htpasswd-ocp4` that now exists in the registry by running:

```bash
apb bundle provision htpasswd-ocp4 -n ocp-workshops --follow
```

The provision command will prompt you for all required parameters to your APB.

__NOTE__: The `--follow` flag used above will show logs produced by the running APB.

You can `deprovision` the `htpasswd-ocp4` that now exists in the registry by running:

```bash
apb bundle deprovision htpasswd-ocp4 -n ocp-workshops --follow
```

The deprovision command will prompt you for the specific instance to delete and all required parameters to your APB.

## Advanced options

|Variable                   | Default Value            | Description   |
|---------------------------|--------------------------|---------------|
|`xxxx`          | true                    | yyyy  |
