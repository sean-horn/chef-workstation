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

This guide contains common configuration options used when setting up a
new Chef Workstation installation. If you do not have Chef Workstation
installed, see its [installation guide](/workstation/install_workstation/)
before proceeding further.

## Prerequisites

1. [Download and install Chef Workstation]({{< relref "install_workstation.md" >}})
1. A running instance of [Chef Infra Server]({{< relref "server/install_server.md" >}}) or [Hosted Chef Server](https://manage.chef.io/signup) set up with at least:
   1. One administrator
   1. One organization
   1. One user
1. Install a [Code Editor]({{< relref "#Code Editors" >}}) (optional)

## Set up Ruby

While you don't have to know the Ruby programming language to use Chef, you do need to make sure that your computer knows where to find Ruby in order to run.

* If you don't plan on writing a lot of Chef cookbooks and you don't use Ruby for other purposes, then set up Ruby with an [environment variable]({{< ref "#Add Ruby as an Environment Variable" >}})
* If you plan on using Ruby for authoring Chef cookbooks but don't use it for other purposes, then we recommend that you use [Chef Workstation's Ruby as the system Ruby]({{< ref "#Use Chef Workstation Ruby as the System Ruby">}}) Use Chef Workstation Ruby as the System Rubyusing the version of Ruby provided in Chef Workstation. Using Chef Workstation's Ruby as your system Ruby is optional.
* If you use enough different versions of Ruby that you use a Ruby version manager such as RVM or rbenv, then make sure that you have the same version of Ruby available as we ship with Chef Workstation. You probably already know how to do it, but `rvm --help` or `rbenv --help` are great places to start.

### Add Ruby as an Environment Variable

Chef Workstation includes a stable version of Ruby as part of its installer.
The path to this version of Ruby must be added to the `$PATH` environment variable and saved in the configuration file for the command shell (Bash, csh, and so on) that is used on the machine running Chef Workstation.

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

{{< warning >}}

On Microsoft Windows, `C:/opscode/Chef Workstation/bin` must be before
`C:/opscode/Chef Workstation/embedded/bin` in the `PATH`.

{{< /warning >}}

### Use Chef Workstation Ruby as the System Ruby

{{< note >}}

These instructions are intended for macOS and Linux users.

{{< /note >}}

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

## Create the Chef repository

{{% chef_repo_description %}}

Use [the chef generate repo]({{< relref "ctl_chef.md#chef-generate-repo" >}}) command to create the Chef repository. For example, to create a repository called
`chef-repo`:

``` bash
chef generate repo chef-repo
```

## Starter Kit

If you have access to Chef Infra Server through Automate or Chef Manage,
you can download the starter kit. The starter kit creates the basic configuration files: the `.chef` directory, `config.rb`, `ORGANIZATION-validator.pem`, and `USER.pem`. Download the starter kit and then move it to the desired location on your computer, which in most cases is your Home directory.


## Configure the Chef Repository

### Chef Infra Server through the WebUI

If you have interact with Chef Infra Server through the web interface (either through Hosted Chef or legacy Chef Manage), follow the following steps to manually set up the chef-repo and to use the Chef management console to get the `.pem` and `config.rb` files.

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
    cp /path/to/config.rb ~/chef-repo/.chef
    ```

    and:

    ``` bash
    cp /path/to/ORGANIZATION-validator.pem ~/chef-repo/.chef
    ```

    and:

    ``` bash
    cp /path/to/USERNAME.pem ~/chef-repo/.chef
    ```

    where `/path/to/` represents the path to the location in which these
    three files were placed after they were downloaded.

2. Verify that the files are in the `.chef` folder.

### Chef Infra Server from the Command Line

In this step, you will retrieve the key files, `ORGANIZATION-validator.pem` and `USER.pem` from your Chef Infra server, and store them in your `.chef` directory.

#### Move Key Files

Download the `ORGANIZATION-validator.pem` and `USER.pem` files from the
Chef Infra Server and move them to the `.chef` directory.

To move files to the `.chef` directory:

1. In a command window, enter each of the following:

    ``` bash
    cp /path/to/ORGANIZATION-validator.pem ~/chef-repo/.chef
    ```

    and:

    ``` bash
    cp /path/to/USERNAME.pem ~/chef-repo/.chef
    ```

    where `/path/to/` represents the path to the location in which these
    three files were placed after they were downloaded.

2. Verify that the files are in the `.chef` folder.

## Set Up Client-to-Server Communication

Navigate to the `~/chef-repo/.chef` directory and create the `config.rb`
using the `knife configure` tool. The file must be created in the
`.chef` folder. It should look similar to:

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

At a minimum, you must update the following settings with the
appropriate values:

- `client_key` should point to the location of the Chef Infra Server
    user's `.pem` file on your Chef Workstation machine.
- `validation_client_name` should be updated with the name of the
    desired organization that was created on the Chef Infra Server.
- `validation_key` should point to the location of your organization's
    `.pem` file on your Chef Workstation machine.
- `chef_server_url` must be updated with the domain or IP address used
    to access the Chef Infra Server.

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

## Ad-hoc remote execution with `chef-run`

The `chef-run` utility allows you to execute ad-hoc configuration updates on the systems you manage without setting up a Chef server. With `chef-run`, you connect to servers over SSH or WinRM, and you can apply single resources, recipes, or even entire cookbooks directly from the command line.

### Example: Installing NTP Server

Chef Workstation combines the power of InSpec and `chef-run`, giving you the ability to easily detect and correct issues on any target instance. One common task that administrators perform in their environments is installing the Network Time Protocol (NTP), which keeps the clocks in sync between servers. InSpec allows us to check if the package is installed with a query, using the InSpec `package` resource:

```ruby
describe package('ntp') do
  it { should be_installed }
end
 ```

Chef also provides a single-resource solution to install the Network Time Protocol package:

```ruby
package 'ntp' do
  action :install
end
```

With `chef-run`, you can run the resource directly from the command-line, converging your targets with a single resource, without creating a cookbook or recipe:

```bash
chef-run myhost package ntp action=install
```

Combined with executing an InSpec scan to validate successful package installation, we have everything we need to define our requirements and make sure they're met with two simple commands, either locally or remotely.

```ruby
inspec exec ntp-check -t ssh://myuser@myhost -i ~/.ssh/mykey
```

```bash
chef-run -i ~/.ssh/mykey myuser@myhost package ntp action=install
```

![Chef Run NTP Installation](/images/chef-workstation/chef-run.gif)

### Recipe and Multi-Node Convergence

Use `chef-run` to execute Chef recipes and cookbooks as well, and run it against multiple targets in parallel. Here are a few  examples of chef-run in action:

#### Example: Recipe execution on multiple targets

Run the default recipe from the defined cookbook against two resources: myhost1 & myhost2.

```bash
chef-run myhost1,myhost2 /path/to/my/cookbook
```

#### Example: Alternate Recipe syntax and targets defined by a range

Run the `my_cookbook::my_recipe` cookbook against twenty resources: myhost1 through myhost20

```bash
chef-run myhost[1:20] my_cookbook::my_recipe
```

## Code Editors

A good visual code editor is not a requirement for working with Chef
Infra, but it can save you time. A code editor should support the following: themes, plugins, snippets, syntax Ruby code coloring/highlighting, multiple cursors, a tree view of the entire folder/repository, and a Git integration.

These are a few common editors:

- [Visual Studio Code (free/open source)](http://code.visualstudio.com)
- [GitHub Atom - (free/open source)](http://atom.io)

Chef Infra support in editors:

- [VSCode Chef Infra Extension](https://marketplace.visualstudio.com/items?itemName=chef-software.Chef)
- [Chef on Atom](https://atom.io/packages/language-chef)

## Further Reading

* [Chef Run CLI Reference]({{< ref "chef_run.md" >}})
* [Introducing Chef Workstation](https://blog.chef.io/2018/05/23/introducing-chef-workstation/)
* [Chef Workstation - How We Made that Demo](https://blog.chef.io/2018/06/25/chef-workstation-how-we-made-that-demo/)
