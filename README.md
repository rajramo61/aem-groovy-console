# AEM Groovy Console

[ICF Next](http://www.icfnext.com)

## Overview

The AEM Groovy Console provides an interface for running [Groovy](http://www.groovy-lang.org/) scripts in Adobe Experience Manager.  Scripts can be created to manipulate content in the JCR, call OSGi services, or execute arbitrary code using the AEM, Sling, or JCR APIs.  After installing the package in AEM (instructions below), see the [console page](http://localhost:4502/apps/groovyconsole.html) for documentation on the available bindings and methods.  Sample scripts are included in the package for reference.

![Screenshot](src/site/screenshot.png)

## Requirements

* AEM author instance running on [localhost:4502](http://localhost:4502/)
* [Maven](http://maven.apache.org/) 3.x

## Compatibility

Groovy Console Version(s) | AEM Version(s)
------------ | -------------
14.x.x, 13.x.x | 6.3, 6.4, 6.5
12.x.x | 6.4
11.x.x | 6.3
10.x.x, 9.x.x | 6.2
8.x.x | 6.1
7.x.x | 6.0
6.x.x, 5.x.x | 5.6 (CQ)
3.x.x | 5.5, 5.4 (CQ)

## Installation

1. [Download the console package](https://github.com/icfnext/aem-groovy-console/releases/download/14.0.0/aem-groovy-console-14.0.0.zip).  For previous versions, tags can be checked out from GitHub and built directly from the source (e.g. `mvn install`).

2. [Verify](http://localhost:4502/apps/groovyconsole.html) the installation.

Additional build profiles may be added in the project's `pom.xml` to support deployment to non-localhost AEM servers.

To enable access to the Groovy Console from `/groovyconsole`, update the **Groovy Console Configuration Service** via the [OSGi console configuration page](http://localhost:4502/system/console/configMgr) to enable the vanity path.

## Building From Source

To build and install the latest development version of the Groovy Console (or if you've made source modifications), run the following Maven command.

    mvn install -P local

## Excluding the Groovy OSGi Bundle

If your AEM instance has multiple applications using Groovy and the `groovy-all` bundle is already deployed, you can exclude this bundle from the Groovy Console package build with the `exclude-groovy-bundle` Maven profile.  This should prevent issues with conflicting Groovy versions at runtime.

    mvn install -P local,exclude-groovy-bundle

## Context Path Support

If you are running AEM with a context path, set the Maven property `aem.context.path` during installation.

    mvn install -P local -Daem.context.path=/context

## OSGi Configuration

Navigate to the [OSGi console configuration page](http://localhost:4502/system/console/configMgr) and select the **Groovy Console Configuration Service**.

Property | Description | Default Value
------------ | ------------- | ----------
Email Enabled? | Check to enable email notification on completion of script execution. | `false`
Email Recipients | Email addresses to receive notification. | `[]`
Allowed Groups | List of group names that are authorized to use the console. If empty, no authorization check is performed. | `[]`
Vanity Path Enabled? | Enables `/groovyconsole` vanity path. | `false`
Audit Disabled? | Disables auditing of script execution history. | `false`
Display All Audit Records? | If enabled, all audit records (including records for other users) will be displayed in the console history. | `false`

## Batch Script Execution

Saved scripts can be remotely executed by sending a POST request to the console servlet with either the `scriptPath` or `scriptPaths` query parameter.

### Single Script

    curl -d "scriptPath=/var/groovyconsole/scripts/samples/JcrSearch.groovy" -X POST -u admin:admin http://localhost:4502/bin/groovyconsole/post.json

### Multiple Scripts

    curl -d "scriptPaths=/var/groovyconsole/scripts/samples/JcrSearch.groovy&scriptPaths=/var/groovyconsole/scripts/samples/FulltextQuery.groovy" -X POST -u admin:admin http://localhost:4502/bin/groovyconsole/post.json

## Extensions

The Groovy Console provides extension hooks to further customize script execution.  The console provides an API containing extension provider interfaces that can be implemented as OSGi services in any bundle deployed to an AEM instance.  See the default extension providers in the `com.icfolson.aem.groovy.console.extension.impl` package for examples of how a bundle can implement these services to supply additional script bindings, compilation customizers, metaclasses, and star imports.

Service Interface | Description
------------ | -------------
`com.icfolson.aem.groovy.console.api.BindingExtensionProvider` | Customize the bindings that are provided for each script execution.
`com.icfolson.aem.groovy.console.api.CompilationCustomizerExtensionProvider` | Restrict language features (via blacklist or whitelist) or provide AST transformations within the Groovy script compilation.
`com.icfolson.aem.groovy.console.api.ScriptMetaClassExtensionProvider` | Add runtime metaclasses (i.e. new methods) to the underlying script class.
`com.icfolson.aem.groovy.console.api.StarImportExtensionProvider` | Supply additional star imports that are added to the compiler configuration for each script execution.

## Notifications

To provide custom notifications for script executions, bundles may implement the `com.icfolson.aem.groovy.console.notification.NotificationService` interface (see the `com.icfolson.aem.groovy.console.notification.impl.EmailNotificationService` class for an example).  These services will be dynamically bound by the Groovy Console service and all registered notification services will be called for each script execution.

## Sample Scripts

Sample scripts can be found in the `src/main/scripts` directory.

## Versioning

Follows [Semantic Versioning](http://semver.org/) guidelines.