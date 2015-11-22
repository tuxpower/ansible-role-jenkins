# jenkins

[![Build Status](https://travis-ci.org/infOpen/ansible-role-jenkins.svg?branch=master)](https://travis-ci.org/infOpen/ansible-role-jenkins)

Install jenkins package.

## Requirements

This role requires Ansible 1.9 or higher, and platform requirements are listed
in the metadata file.

## Testing

This role has two test methods :

- localy with Vagrant :
    vagrant up

- automaticaly by Travis

Vagrant should be used to check the role before push changes to Github.

## Role Variables

Follow the possible variables with their default values

### Defaults file for jenkins

    # Ubuntu repository vars
    jenkins_repository_key_url : "https://jenkins-ci.org/debian/jenkins-ci.org.key"
    jenkins_package_state      : "latest"
    jenkins_repository_content : "deb http://pkg.jenkins-ci.org/debian binary/"

    jenkins_system_dependencies : []
    jenkins_system_dependencies_state : "present"

    # Configuration file settings
    jenkins_default_cfg_file_owner : root
    jenkins_default_cfg_file_group : root
    jenkins_default_cfg_file_mode  : "0640"

    # Configuration file content variables
    jenkins_etc_name  : "jenkins"
    jenkins_etc_user  : "jenkins"
    jenkins_etc_group : "jenkins"
    jenkins_etc_run_standalone : True
    jenkins_etc_max_open_files : 8192
    jenkins_etc_umask          : "022"
    jenkins_etc_listen_address : 127.0.0.1
    jenkins_etc_http_port      : 8080
    jenkins_etc_ajp_port       : -1
    jenkins_etc_servlet_context_prefix : "/{{ jenkins_etc_name }}"

    # Location and files configuration
    jenkins_etc_java_location : "/usr/bin/java"
    jenkins_etc_war_location  : >
      /usr/share/{{ jenkins_etc_name }}/{{ jenkins_etc_name }}.war
    jenkins_etc_home_location : "/var/lib/{{ jenkins_etc_name }}"
    jenkins_etc_log_location  : >
      /var/log/{{ jenkins_etc_name }}/{{ jenkins_etc_name }}.log
    jenkins_etc_pid_file      : >
      /var/run/{{ jenkins_etc_name }}/{{ jenkins_etc_name }}.pid

    # Java and jenkins arguments
    jenkins_etc_java_args :
      - "-Djava.awt.headless=true"
    jenkins_etc_args :
      - "--webroot=/var/cache/{{ jenkins_etc_name }}/war"
      - "--httpPort={{ jenkins_etc_http_port }}"
      - "--ajp13Port={{ jenkins_etc_ajp_port }}"

    # Jenkins cli
    jenkins_base_url         : "http://localhost:{{ jenkins_etc_http_port }}"
    jenkins_cli_download_url : "{{ jenkins_base_url }}/jnlpJars/jenkins-cli.jar"
    jenkins_cli : "{{ jenkins_etc_home_location }}/jenkins-cli.jar"

    # Jenkins update center variables
    jenkins_update_center_url_download : >
      https://updates.jenkins-ci.org/update-center.json
    jenkins_update_center_url_post : >
      {{ jenkins_base_url }}/updateCenter/byId/default/postBack
    jenkins_update_file : "{{ jenkins_etc_home_location }}/updates_jenkins.json"

    # Jenkins waiting availability test
    jenkins_waiting_available_retries : 10
    jenkins_waiting_available_delay   : 5

    # Plugins
    jenkins_plugins : []

    # API URLs
    #---------
    jenkins_api_plugins_list : >
      pluginManager/api/json?depth=1&tree=plugins[shortName,version]

    # CONFIGURATION
    #--------------
    jenkins_configuration_files_owner : "{{ jenkins_etc_user }}"
    jenkins_configuration_files_group : "{{ jenkins_etc_group }}"
    jenkins_configuration_files_mode  : "0644"

    # Main configuration
    jenkins_main_cfg_version : ''
    jenkins_main_cfg_num_executors : 2
    jenkins_main_cfg_mode : 'NORMAL'
    jenkins_main_cfg_use_security : True
    jenkins_main_cfg_authorization_strategy : >
      hudson.security.AuthorizationStrategy$Unsecured
    jenkins_main_cfg_security_realm : 'hudson.security.SecurityRealm$None'
    jenkins_main_cfg_disable_remember_me : False
    jenkins_main_cfg_project_naming_strategy : >
      jenkins.model.ProjectNamingStrategy$DefaultProjectNamingStrategy
    jenkins_main_cfg_workspace_dir : '${ITEM_ROOTDIR}/workspace'
    jenkins_main_cfg_builds_dir : '${ITEM_ROOTDIR}/builds'
    jenkins_main_cfg_quiet_period : 5
    jenkins_main_cfg_scm_checkout_retry_count : 0
    jenkins_main_cfg_views : []
    jenkins_main_cfg_primary_view : All
    jenkins_main_cfg_slave_agent_port : 0
    jenkins_main_cfg_label : ''

    # Misc configuration
    jenkins_misc_cfg :
      - artifact-manager
      - global-maven
      - jenkins-location
      - maven
      - scm-trigger
      - shell

    # Misc : artifact manager
    jenkins_misc_cfg_artifact_manager_factories : []

    # Misc : global maven
    jenkins_misc_cfg_jenkins_global_maven_settings_provider : {}
    jenkins_misc_cfg_jenkins_global_maven_global_settings_provider : {}

    # Misc : jenkins location
    jenkins_misc_cfg_jenkins_location_admin_address : ""
    jenkins_misc_cfg_jenkins_location_url : "{{ jenkins_base_url }}/"

    # Misc : maven
    jenkins_misc_cfg_maven_installations : []

    # Misc : scm-trigger
    jenkins_misc_cfg_scm_trigger_synchronous_polling : False
    jenkins_misc_cfg_scm_trigger_maximum_threads : 0

    # Misc : shell
    jenkins_misc_cfg_shells : []

    # Misc templates
    jenkins_misc_templates :
      - name     : artifact-manager
        template : "jenkins.model.ArtifactManagerConfiguration.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location -}}
          /jenkins.model.ArtifactManagerConfiguration.xml
      - name     : global-maven
        template : "jenkins.mvn.GlobalMavenConfig.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location -}}/jenkins.mvn.GlobalMavenConfig.xml
      - name     : jenkins-location
        template : "jenkins.model.JenkinsLocationConfiguration.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location -}}
          /jenkins.model.JenkinsLocationConfiguration.xml
      - name     : maven
        template : "hudson.tasks.Maven.xml.j2"
        dest     : "{{ jenkins_etc_home_location }}/hudson.tasks.Maven.xml"
      - name     : shell
        template : "hudson.tasks.Shell.xml.j2"
        dest     : "{{ jenkins_etc_home_location }}/hudson.tasks.Shell.xml"

    # Plugins templates
    jenkins_plugin_templates :
      - name     : ansible
        template : "org.jenkinsci.plugins.ansible.AnsibleInstallation.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location -}}
          /org.jenkinsci.plugins.ansible.AnsibleInstallation.xml
      - name     : ansicolor
        template : "hudson.plugins.ansicolor.AnsiColorBuildWrapper.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location -}}
          /hudson.plugins.ansicolor.AnsiColorBuildWrapper.xml
      - name     : ant
        template : "hudson.tasks.Ant.xml.j2"
        dest     : "{{ jenkins_etc_home_location }}/hudson.tasks.Ant.xml"
      - name     : cvs
        template : "hudson.scm.CVSSCM.xml.j2"
        dest     : "{{ jenkins_etc_home_location }}/hudson.scm.CVSSCM.xml"
      - name     : debian-package-builder
        template : >
          ru.yandex.jenkins.plugins.debuilder.DebianPackageBuilder.xml.j2
        dest     : >
          {{ jenkins_etc_home_location -}}
          /ru.yandex.jenkins.plugins.debuilder.DebianPackageBuilder.xml
      - name     : debian-package-builder
        template : >
          ru.yandex.jenkins.plugins.debuilder.DebianPackagePublisher.xml.j2
        dest     : >
          {{ jenkins_etc_home_location -}}
          /ru.yandex.jenkins.plugins.debuilder.DebianPackagePublisher.xml
      - name     : envinject
        template : "envInject.xml.j2"
        dest     : "{{ jenkins_etc_home_location }}/envInject.xml"
      - name     : envinject
        template : "envinject-plugin-configuration.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location }}/envinject-plugin-configuration.xml
      - name     : github
        template : "github-plugin-configuration.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location }}/github-plugin-configuration.xml
      - name     : git
        template : "hudson.plugins.git.GitSCM.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location }}/hudson.plugins.git.GitSCM.xml
      - name     : git-client
        template : "org.jenkinsci.plugins.gitclient.JGitTool.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location }}
          /org.jenkinsci.plugins.gitclient.JGitTool.xml }}
      - name     : graphiteIntegrator
        template : >
          org.jenkinsci.plugins.graphiteIntegrator.GraphitePublisher.xml.j2
        dest     : >
          {{ jenkins_etc_home_location -}}
          /org.jenkinsci.plugins.graphiteIntegrator.GraphitePublisher.xml
      - name     : mailer
        template : "hudson.tasks.Mailer.xml.j2"
        dest     : "{{ jenkins_etc_home_location }}/hudson.tasks.Mailer.xml"
      - name     : maven-plugin
        template : "hudson.maven.MavenModuleSet.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location }}/hudson.maven.MavenModuleSet.xml
      - name     : publish-over-ssh
        template : >
          jenkins.plugins.publish_over_ssh.BapSshPublisherPlugin.xml.j2
        dest     : >
          {{ jenkins_etc_home_location -}}
          /jenkins.plugins.publish_over_ssh.BapSshPublisherPlugin.xml
      - name     : redmine
        template : "hudson.plugins.redmine.RedmineProjectProperty.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location -}}
          /hudson.plugins.redmine.RedmineProjectProperty.xml
      - name     : ssh
        template : "org.jvnet.hudson.plugins.SSHBuildWrapper.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location -}}
          /org.jvnet.hudson.plugins.SSHBuildWrapper.xml
      - name     : subversion
        template : "hudson.scm.SubversionSCM.xml.j2"
        dest     : >
          {{ jenkins_etc_home_location -}}
          /hudson.scm.SubversionSCM.xml

    # Plugins : ansible
    jenkins_plugin_cfg_ansible_installations : []

    # Plugins : ansicolor
    jenkins_plugin_cfg_ansicolor_maps :
      - name : xterm
        normal :
          - [ "BLACK",   "#000000" ]
          - [ "RED",     "#CD0000" ]
          - [ "GREEN",   "#00CD00" ]
          - [ "YELLOW",  "#CDCD00" ]
          - [ "BLUE",    "#1E90FF" ]
          - [ "MAGENTA", "#CD00CD" ]
          - [ "CYAN",    "#00CDCD" ]
          - [ "WHITE",   "#E5E5E5" ]
        bright :
          - [ "BLACK",   "#4C4C4C" ]
          - [ "RED",     "#FF0000" ]
          - [ "GREEN",   "#00FF00" ]
          - [ "YELLOW",  "#FFFF00" ]
          - [ "BLUE",    "#4682B4" ]
          - [ "MAGENTA", "#FF00FF" ]
          - [ "CYAN",    "#00FFFF" ]
          - [ "WHITE",   "#FFFFFF" ]
      - name : vga
        normal :
          - [ "BLACK",   "#000000" ]
          - [ "RED",     "#AA0000" ]
          - [ "GREEN",   "#00AA00" ]
          - [ "YELLOW",  "#AA5500" ]
          - [ "BLUE",    "#0000AA" ]
          - [ "MAGENTA", "#AA00AA" ]
          - [ "CYAN",    "#00AAAA" ]
          - [ "WHITE",   "#AAAAAA" ]
        bright :
          - [ "BLACK",   "#555555" ]
          - [ "RED",     "#FF5555" ]
          - [ "GREEN",   "#55FF55" ]
          - [ "YELLOW",  "#FFFF55" ]
          - [ "BLUE",    "#5555FF" ]
          - [ "MAGENTA", "#FF55FF" ]
          - [ "CYAN",    "#55FFFF" ]
          - [ "WHITE",   "#FFFFFF" ]
        default_foreground : 7
        default_background : 0
      - name : css
        normal :
          - [ "BLACK",   "black"   ]
          - [ "RED",     "red"     ]
          - [ "GREEN",   "green"   ]
          - [ "YELLOW",  "yellow"  ]
          - [ "BLUE",    "blue"    ]
          - [ "MAGENTA", "magenta" ]
          - [ "CYAN",    "cyan"    ]
          - [ "WHITE",   "white"   ]
        bright :
          - [ "BLACK",   "black"   ]
          - [ "RED",     "red"     ]
          - [ "GREEN",   "green"   ]
          - [ "YELLOW",  "yellow"  ]
          - [ "BLUE",    "blue"    ]
          - [ "MAGENTA", "magenta" ]
          - [ "CYAN",    "cyan"    ]
          - [ "WHITE",   "white"   ]
      - name : gnome-terminal
        normal :
          - [ "BLACK",   "#2E3436" ]
          - [ "RED",     "#CC0000" ]
          - [ "GREEN",   "#4E9A06" ]
          - [ "YELLOW",  "#C4A000" ]
          - [ "BLUE",    "#3465A4" ]
          - [ "MAGENTA", "#75507B" ]
          - [ "CYAN",    "#06989A" ]
          - [ "WHITE",   "#D3D7CF" ]
        bright :
          - [ "BLACK",   "#2E3436" ]
          - [ "RED",     "#CC0000" ]
          - [ "GREEN",   "#4E9A06" ]
          - [ "YELLOW",  "#C4A000" ]
          - [ "BLUE",    "#3465A4" ]
          - [ "MAGENTA", "#75507B" ]
          - [ "CYAN",    "#06989A" ]
          - [ "WHITE",   "#D3D7CF" ]
        default_foreground : 7
        default_background : 0

    # Plugins : ant
    jenkins_plugin_cfg_ant_installations : []

    # Plugins : cvs
    jenkins_plugin_cfg_cvs_generation : 1
    jenkins_plugin_cfg_cvs_compression_level : 3
    jenkins_plugin_cfg_cvs_private_key_location : ""
    jenkins_plugin_cfg_cvs_private_key_password : ""
    jenkins_plugin_cfg_cvs_known_hosts_location : ""
    jenkins_plugin_cfg_cvs_auth_tokens : []
    jenkins_plugin_cfg_cvs_changelog_encoding : UTF-8

    # Plugins : debian-package-builder
    jenkins_plugin_cfg_debian_package_builder_public_key    : ""
    jenkins_plugin_cfg_debian_package_builder_private_key   : ""
    jenkins_plugin_cfg_debian_package_builder_account_name  : "Jenkins"
    jenkins_plugin_cfg_debian_package_builder_account_email : ""
    jenkins_plugin_cfg_debian_package_builder_passphrase    : ""
    jenkins_plugin_cfg_debian_package_builder_repos : []

    # Plugin : envinject
    jenkins_plugin_cfg_envinject_global_password_entries : []
    jenkins_plugin_cfg_envinject_hide_injected_vars : False
    jenkins_plugin_cfg_envinject_enable_permissions : False

    # Plugins : github
    jenkins_plugin_cfg_github_configs : []

    # Plugins : git
    jenkins_plugin_cfg_git_generation   : 1
    jenkins_plugin_cfg_git_global_name  : foobar
    jenkins_plugin_cfg_git_global_email : "foo@foo.bar"
    jenkins_plugin_cfg_git_create_account_based_on_email : False

    # Plugins : git-client
    jenkins_plugin_cfg_git_client_installations : []

    # Plugins : graphiteIntegrator
    jenkins_plugin_cfg_graphite_integrator_servers : []
    jenkins_plugin_cfg_graphite_integrator_metrics_map : []
    jenkins_plugin_cfg_graphite_integrator_base_queue_name : ""

    # Plugins : mailer
    jenkins_plugin_cfg_mailer_use_ssl : False
    jenkins_plugin_cfg_mailer_charset : UTF-8

    # Plugins : maven-plugin
    jenkins_plugin_cfg_maven_plugin_local_repository  : {}
    jenkins_plugin_cfg_maven_plugin_validation_levels :
      - level_string : DEFAULT
        level_int    : -1
      - level_string : LEVEL_MINIMAL
        level_int    : 0
      - level_string : LEVEL_MAVEN_2_0
        level_int    : 20
      - level_string : LEVEL_MAVEN_3_0
        level_int    : 30
      - level_string : LEVEL_MAVEN_3_1
        level_int    : 31
      - level_string : LEVEL_STRICT
        level_int    : 30

    # Plugins : publish-over-ssh
    jenkins_plugin_cfg_publish_over_ssh_defaults : []
    jenkins_plugin_cfg_publish_over_ssh_host_configurations : []
    jenkins_plugin_cfg_publish_over_ssh_common_passphrase : ""
    jenkins_plugin_cfg_publish_over_ssh_common_key : ""
    jenkins_plugin_cfg_publish_over_ssh_common_key_path : ""
    jenkins_plugin_cfg_publish_over_ssh_common_disable_all_exec : False

    # Plugins : redmine
    jenkins_plugin_cfg_redmine_websites : []

    # Plugins : ssh
    jenkins_plugin_cfg_ssh_sites : []

    # Plugins : subversion
    jenkins_plugin_cfg_subversion_generation : 1
    jenkins_plugin_cfg_subversion_legacy_per_job_credentials : False
    jenkins_plugin_cfg_subversion_workspace_format : 8
    jenkins_plugin_cfg_subversion_validate_remote_up_to_var : False
    jenkins_plugin_cfg_subversion_store_auth_to_disk : False

### How configure a Docker cloud
    jenkins_main_cfg_clouds:
      - type: 'docker-plugin'
        name: 'my-docker-cloud'
        url: 'http://127.0.0.1:8081'
        connect_timeout: 0
        read_timeout: 0
        credentials_id: ''
        container_cap: 100
        templates:
          - config_version: 2
            label_string: ''
            remote_fs_mapping: '/tmp'
            remote_fs: '/home/jenkins'
            instance_cap: 1
            mode: 'NORMAL'
            num_executor: 1
            remove_volumes: False
            pull_strategy: 'PULL_LATEST'
            launcher:
              class: 'ssh'
              port: 22
              credentials_id: ''
              jvm_options: []
              java_path: ''
              max_num_retries: 0
              retry_wait_time: 0
            template_base:
              image: 'evarga/jenkins-slave'
              docker_command: ''
              lxc_conf_string: ''
              hostname: ''
              dns_hosts: []
              volumes: []
              volumes_from2: []
              environment: []
              bind_ports: []
              bind_all_ports: False
              privileged: False
              tty: False
              extra_hosts: []
              mac_address: ''
              memory_limit: 0
              cpu_shares: 0
            retention_strategy:
              idle_minutes: 10

### Specific vars values for Debian family

    jenkins_repository_file_prefix : "/etc/apt/sources.list.d"
    jenkins_repository_file        : "pkg_jenkins_ci_org_debian.list"

    jenkins_default_cfg_prefix : "/etc/default"
    jenkins_default_cfg_file   : "jenkins"

    jenkins_package_name : "jenkins"
    jenkins_service_name : "jenkins"

    jenkins_system_dependencies :
      - python-httplib2

## Dependencies

- achaussier.openjdk-jre
- achaussier.openjdk-jdk

## Example Playbook

    - hosts: servers
      roles:
         - { role: achaussier.jenkins }

## License

MIT

## Author Information

Alexandre Chaussier (for Infopen company)
- http://www.infopen.pro
- a.chaussier [at] infopen.pro
