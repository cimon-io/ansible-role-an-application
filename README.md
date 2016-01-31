Admin users role
=========

Example Playbook
----------------

    - role: an-application
      application_name: any
      project_repo: git@gitlab.cssum.net:all/any.git
      processes:
        - name: pubsub
          process: npm start
        - name: server
          process: npm run server
      env:
        - ENV1=1
        - ENV2=2
        - RACK_ENV=production
      release_version: "master"
      shared:
        - src: .env
          dest: .env
        - src: node_modules
          dir: yes
          dest: node_modules
      prepare:
        - npm install
      application_dependent_packages:
        - mc
        - htop
        - ruby
        - nodejs

It creates `/var/www/any` folder with `releases`, `shared` folders and `current` symlink. Also it created upstart scripts for each process with name `any-pubsub` and `any-server`. Each upstart script will contain environment definitions.
