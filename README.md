# An-application ansible role

An ansible role that allows to deploy applications.

The role includes the following tasks:
1. Create application directories at `application_prefix/application_name` with `releases` and `shared` folders inside. Get the current time for a timestamp.
2. Create additional shared directories for `application_shared` items.
3. Configure `application_environment` variables if specified.
4. Download a project from git `application_repository` or `application_url` to `releases`.
5. Link shared resources to the application.
6. Execute specified in `application_prepare` commands to prepare the application.
7. Link the deployed application to the current directory `application_prefix/application_name/current`.
8. Keep last `application_keep_versions` releases. Cleanup unnecessary files.

This role can be run under all versions of Ubuntu.

## Requirements

None

## Role Variables

All available variables are listed below along with default values (see `defaults/main.yml`). The next variables specify a user and a group you will run scripts as:

```yaml
application_user: "deploy"
application_group: "deploy"
```

Use following variables to set the application name and prefix. All application directories will be created at `application_prefix/application_name`.

```yaml
application_prefix: "/var/www/"
application_name: ""
```

You can define how many last releases should be kept with the next variable:

```yaml
application_keep_versions: 3   # Keep last 3 releases
```

If `application_repository` is defined, the `application_deploy_key` values is sent to a remote deploy user at `shared/.ssh_deploy_key`. Then the project repository is cloned from git `application_repository`. You can define what release version of the repository should be used with the appropriate variable. It can be a literal string HEAD (default), a branch name or a tag name.

```yaml
application_repository: ""       # Address of a git repository
application_deploy_key: ""       # An optional private key file for the repository
application_release_version: ""  # Version to check out
```

If `application_url` is specified, the role creates application directory at `releases/timestamp` and downloads a project archive from `application_url` to it. If necessary, you can set HTTP headers for authentication.

```yaml
application_url: ""
application_url_headers: ""  # Custom HTTP headers to a request in the format "key:value,key:value"
```

A list of shared directories, which will be created, can be set by the `application_shared` variable.

```yaml
application_shared: []
```

Available parameters for each item are given below:

```yaml
application_shared:
  - src: public   # Link 'shared/src' to 'releases/timestamp/dest'
    dest: public
    dir: yes      # Create shared directories for the item
```

If `application_environment` variables are specified, the role attaches them to the application at `shared/.env` and export the values to `$HOME/.profile`.

```yaml
application_environment: []
```

You can list required variables in the following format:

```yaml
application_environment:
  - ENV1=VALUE1
  - ENV2=VALUE2
```

Specify other commands which must be executed for the application deployment as a list of strings:

```yaml
application_prepare: []
# application_prepare:
#   - "unzip archive.zip"
#   - "rm archive.zip"
```

Set a list of services which should be restarted after the application deployment.

```yaml
application_services: []
# application_services:
#   - appname.service
#   - "{{ systemd_service_name }}"
```

## Dependencies

None

## Example playbook

The given example creates `/var/www/any` folder with `releases`, `shared` folders and `current` symlink.

```yaml
    - role: an-application
      application_name: any
      application_repository: git@gitlab.cssum.net:all/any.git
      application_environment:
        - ENV1=1
        - ENV2=2
        - RACK_ENV=production
      application_release_version: "master"
      application_shared:
        - src: .env
          dest: .env
        - src: node_modules
          dir: yes
          dest: node_modules
      application_prepare:
        - npm install
```

## License

Licensed under the [MIT License](https://opensource.org/licenses/MIT).
