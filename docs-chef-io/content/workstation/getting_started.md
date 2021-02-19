+++
title = "Getting Started"
draft = false

gh_repo = "chef-workstation"

aliases = ["/workstation_setup.html", "/chefdk_setup.html", "/workstation.html", "/workstation_setup/"]

[menu]
  [menu.workstation]
    title = "Getting Started"
    identifier = "chef_workstation/getting_started.md Getting Started"
    parent = "chef_workstation"
    weight = 40
+++

This guide walks your through setting up Chef Workstation on your computer.

* [Setup Ruby for Chef Workstation]({{< relref "#Setup Ruby for Chef Workstation" >}})
* [Set up your chef-repo]({{< relref "#Setup Your Chef Repo" >}}) for storing your cookbooks
* [Understand your Chef Workstation installation]({{< relref "#Your Chef Workstation Installation" >}})
* [Set up communication between Chef Workstation and Chef Infra Server]({{< relref "#Setup Chef Workstation and Chef Infra Server Communication" >}}) by transferring keys and configuring [knife]{{< relref "/workstation/knife.md" >}}.

In order to use Chef Workstation effectively, you need to install and set up [Chef Infra Server]({{< relref "/server/install_server.md" >}})
If you do not have Chef Workstation installed, see the [installation guide]({{< relref "/workstation/install_workstation.md" >}}) before proceeding further.

## Prerequisites

1. [Download and install Chef Workstation]({{< relref "install_workstation.md" >}})
1. A running instance of [Chef Infra Server]({{< relref "server/install_server.md" >}}) or [Hosted Chef Server](https://manage.chef.io/signup) and access to the:
   1. `ORGANIZATION-validator.pem`
   1. `USER.pem`
1. Install a [Code Editor]({{< relref "#Code Editors" >}}) (optional)

## Setup Ruby for Chef Workstation

While you don't have to know the Ruby programming language to use Chef, you do need to make sure that your computer knows where to find Ruby in order to run.

* If you don't plan on writing a lot of Chef cookbooks and you're not a Ruby developer, then we recommend that you set [Chef Workstation's Ruby as the system Ruby]({{< relref "#Use Chef-Workstation Ruby as the System Ruby">}})
* If you are a Ruby developer, then set up Ruby with an [environment variable]({{< relref "#Add Ruby as an Environment Variable" >}}).

### Use Chef Workstation Ruby as the System Ruby
<!---- Tabs Section--->
{{< foundation_tabs tabs-id="tabs-panel-container" >}}
{{< foundation_tab active="true" panel-link="ruby-macOS-panel" tab-text="macOS">}}
{{< foundation_tab panel-link="ruby-windows-panel" tab-text="Windows" >}}
{{< /foundation_tabs >}}
<!---- End Tabs --->

<!---- Panels Section --->
{{< foundation_tabs_panels tabs-id="tabs-panel-container" >}}
{{< foundation_tabs_panel active="true" panel-id="ruby-macOS-panel" >}}

1. Open a terminal and enter:

    ``` bash
    which ruby
    ```

    which should return something like `/usr/bin/ruby`.

1. To use the Ruby provided by Chef Workstation as the default Ruby on your system, edit the `$PATH` and `GEM` environment variables to include paths to Chef Workstation. For example, on a machine that runs Bash, run:

    ``` bash
    echo 'eval "$(chef shell-init bash)"' >> ~/.bash_profile
    ```

    where `bash` and `~/.bash_profile` represents the name of the shell.

    If zsh is your preferred shell then run the following:

    ``` bash
    echo 'eval "$(chef shell-init zsh)"' >> ~/.zshrc
    ```

3. Check the Ruby version again:

   ``` bash
   which ruby
   ```

   should return:

   ``` bash
   /opt/chef-workstation/embedded/bin/ruby`
   ```

{{< /foundation_tabs_panel >}}
{{< foundation_tabs_panel panel-id="ruby-windows-panel" >}}

NEED WINDOWS EXAMPLE

{{< /foundation_tabs_panel >}}

{{< /foundation_tabs_panels >}}
<!---- End Panels --->
### Add Ruby as an Environment Variable

Chef Workstation includes a stable version of Ruby as part of its installer.
The path to this version of Ruby must be added to the `$PATH` environment variable and saved in the configuration file for the command shell (Bash, csh, and so on) that is used on the machine running Chef Workstation.

<!---- Tabs Section--->
{{< foundation_tabs tabs-id="tabs-panel-container" >}}
{{< foundation_tab active="true" panel-link="ruby-env-macOS-panel" tab-text="macOS">}}
{{< foundation_tab panel-link="ruby-env-windows-panel" tab-text="Windows" >}}
{{< /foundation_tabs >}}
<!---- End Tabs --->

<!---- Panels Section --->
{{< foundation_tabs_panels tabs-id="tabs-panel-container" >}}
{{< foundation_tabs_panel active="true" panel-id="ruby-env-macOS-panel" >}}
In a terminal window, enter:

``` bash
echo 'export PATH="/opt/chef-workstation/embedded/bin:$PATH"' >> ~/.configuration_file && source ~/.configuration_file
```

where `configuration_file` is the name of the configuration file for the
specific command shell. For example, if Bash were the command shell and
the configuration file were named `bash_profile`, the command would look
something like the following:

``` bash
echo 'export PATH="/opt/chef-workstation/embedded/bin:$PATH"' >> ~/.bash_profile && source ~/.bash_profile
```

{{< /foundation_tabs_panel >}}
{{< foundation_tabs_panel panel-id="ruby-env-windows-panel" >}}
{{< warning >}}

On Microsoft Windows, `C:/opscode/Chef Workstation/bin` must be before
`C:/opscode/Chef Workstation/embedded/bin` in the `PATH`.

{{< /warning >}}
{{< /foundation_tabs_panel >}}

{{< /foundation_tabs_panels >}}

## Setup Your Chef Repo

{{% chef_repo_description %}}

Use [the chef generate repo]({{< relref "ctl_chef.md#chef-generate-repo" >}}) command to create the Chef repository. For example, to create a repository called
`chef-repo`:

``` bash
chef generate repo chef-repo
```

## Your Chef Workstation Installation

Chef Workstation installs two different things onto your computer:

1. [Chef products and tools]({{< relref "install_workstation.md" >}})
1. The `~/.chef` directory

The `~/.chef` directory starts out with one file, an example `config.rb`.

## Setup Chef Workstation and Chef Infra Server Communication

To set up Chef Workstation up, you need to add the following files to your `~.chef` directory:

* ORGANIZATION-validator.pem
* USERNAME.pem
* a completed `config.rb`

### Chef Infra Server through the WebUI

If you have interact with Chef Infra Server through the web interface (either through Hosted Chef or legacy Chef Manage), follow the following steps to manually set up the chef-repo and to use the Chef management console to get the `.pem` and `config.rb` files.

<!---- Tabs Section--->
{{< foundation_tabs tabs-id="tabs-panel-container" >}}
{{< foundation_tab active="true" panel-link="webui-macOS-panel" tab-text="macOS">}}
{{< foundation_tab panel-link="webui-win-panel" tab-text="Windows" >}}

{{< /foundation_tabs >}}
<!---- End Tabs --->

<!---- Panels Section --->
{{< foundation_tabs_panels tabs-id="tabs-panel-container" >}}
{{< foundation_tabs_panel active="true" panel-id="webui-macOS-panel" >}}

#### Get Config Files

For a Chef Workstation installation that will interact with the Chef
Infra Server (including the hosted Chef Infra Server), log on and
download the following files:

- `config.rb`. This configuration file can be downloaded from the **Organizations** page.
- `ORGANIZATION-validator.pem`. This private key can be downloaded from the **Organizations** page.
- `USER.pem`. This private key can be downloaded from the **Change Password** section of the **Account Management** page.

#### Move Config Files

The `config.rb`, `ORGANIZATION-validator.pem`, and `USER.pem` files must
be moved to the `.chef` directory after they are downloaded from the
Chef Infra Server.

To move files to the `.chef` directory:

1. In a command window, enter each of the following:

    ``` bash
    cp /path/to/config.rb ~/.chef
    ```

    and:

    ``` bash
    cp /path/to/ORGANIZATION-validator.pem ~/.chef
    ```

    and:

    ``` bash
    cp /path/to/USERNAME.pem ~/.chef
    ```

    where `/path/to/` represents the path to the location in which these
    three files were placed after they were downloaded.

2. Verify that the files are in the `.chef` folder.
{{< /foundation_tabs_panel >}}
{{< foundation_tabs_panel panel-id="webui-win-panel" >}}

{{< /foundation_tabs_panel >}}

{{< /foundation_tabs_panels >}}

### Chef Infra Server from the Command Line

In this step, you will retrieve the key files, `ORGANIZATION-validator.pem` and `USER.pem` from your Chef Infra server, and store them in your `.chef` directory.
<!---- Tabs Section--->
{{< foundation_tabs tabs-id="tabs-panel-container" >}}
{{< foundation_tab active="true" panel-link="server-macos-panel" tab-text="macOS">}}
{{< foundation_tab panel-link="server-win-panel" tab-text="Windows" >}}
{{< /foundation_tabs >}}
<!---- End Tabs --->

<!---- Panels Section --->
{{< foundation_tabs_panels tabs-id="tabs-panel-container" >}}
{{< foundation_tabs_panel active="true" panel-id="server-macOS-panel" >}}

#### Move Key Files

Download the `ORGANIZATION-validator.pem` and `USER.pem` files from the
Chef Infra Server and move them to the `.chef` directory.

To move files to the `.chef` directory:

1. In a command window, enter each of the following:

    ``` bash
    cp /path/to/ORGANIZATION-validator.pem ~/.chef
    ```

    and:

    ``` bash
    cp /path/to/USERNAME.pem ~/chef-repo/.chef
    ```

    where `/path/to/` represents the path to the location in which these
    three files were placed after they were downloaded.

2. Verify that the files are in the `.chef` folder.
{{< /foundation_tabs_panel >}}
{{< foundation_tabs_panel panel-id="server-win-panel" >}}

{{< /foundation_tabs_panel >}}

{{< /foundation_tabs_panels >}}

## Set Up Chef Infra Client-to-Server Communication

Navigate to the `~/.chef` directory and set up the `config.rb`
with your information tool.

At a minimum, you must update the following settings with the
appropriate values:

- `client_key` is the location of the Chef Infra Server user's `.pem` file on your Chef Workstation machine.
- `validation_client_name` should be updated with the name of the desired organization that was created on the Chef Infra Server.
- `validation_key` should point to the location of your organization's `.pem` file on your Chef Workstation machine.
- `chef_server_url` must be updated with the domain or IP address used to access the Chef Infra Server.

It should look similar to:

``` ruby
current_dir = File.dirname(__FILE__)
log_level                :info
log_location             STDOUT
node_name                'node_name'
client_key               "#{current_dir}/USER.pem"
validation_client_name   'ORG_NAME-validator'
validation_key           "#{current_dir}/ORGANIZATION-validator.pem"
chef_server_url          'https://api.chef.io/organizations/ORG_NAME'
cache_type               'BasicFile'
cache_options( :path => "#{ENV['HOME']}/.chef/checksums" )
cookbook_path            ["#{current_dir}/../cookbooks"]
```

See the [knife config.rb documentation](/workstation/config_rb/) for more
details.

## Get SSL Certificates

All communication from Chef Workstation to the Chef Infra Server includes SSL verification for security purposes. The Chef Infra Server setup process will automatically generate a self-signed certificate for the server. If this certificate is not replaced with a certificate signed by a certificate authority (CA) your Chef Workstation installation will not be able to verify the certificate by default. To trust self-signed certificates you can use the `knife ssl fetch` subcommand to download the SSL certificate from the Chef Infra Server:

``` bash
knife ssl fetch
```

See [SSL Certificates](/chef_client_security/#ssl-certificates) for more information about how knife and Chef Infra Client use SSL certificates generated by the Chef Infra Server.

### Verify Client-to-Server Communication

To verify that Chef Workstation can connect to the Chef Infra Server:

1. In a command window, navigate to the Chef repository:

    ``` bash
    cd ~/chef-repo
    ```

2. In a command window, enter the following:

    ``` bash
    knife client list
    ```

    to return a list of clients (registered nodes and Chef Workstation
    installations) that have access to the Chef Infra Server. For
    example:

    ``` bash
    chefdk_machine
    registered_node
    ```
