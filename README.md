# Engine Yard Cloud and Jenkins


## Getting Started

You can boot Jenkins into a new Engine Yard Cloud environment with two simple commands:

```
gem install ey_cli
ey_cli create_app --git git://github.com/engineyard/eycloud-app-jenkins.git --name jenkins --env_name jenkins --type rack
```

When the environment finishes booting, you can view it with:

```
ey launch -e jenkins -a jenkins
```

When the instance finished booting and deploying you will have Jenkins running!

### xvfb

`xvfb-run` was installed in `/engineyard/bin` and is the script that you will actually execute in Jenkins.

The hello world example:

    $ /engineyard/bin/xvfb-run -s "-screen 0 1024x768x24" echo "Hello world"

You'll want to use the following code chunk in your Jenkins jobs:

    #!/bin/bash
    export RAILS_ENV=test
    cp /data/jenkins/shared/config/database.yml config/
    sed -i 's/jenkins/test/' config/database.yml
    sed -i 's/production:/test:/' config/database.yml
    bundle --deployment
    /engineyard/bin/xvfb-run -s "-screen 0 1024x768x24" bundle exec rake db:create db:migrate spec --trace
    

Some `sed` was necessary to prepare the `database.yml` file for a test database. The first `sed` renames the database from whatever you named this CI application to test. The second `sed` just mods the existing production environment section to be test. Production is the EY default.

## Other packages installed

* qt-webkit 4.7.3 - as required for [capybara-webkit](https://github.com/thoughtbot/capybara-webkit)
* xorg-server 1.3 - headless X 

## How this was created

    gem install engineyard-recipes
    ey-recipes init --on-deploy --sm # todo - need the emerge package here
    ey-recipes sm git@github.com:engineyard/sm_eyapi.git -n eyapi
    ey-recipes sm git@github.com:engineyard/sm_jenkins.git install configure restart -n jenkins
    ey-recipes package qt-webkit -p x11-libs/qt-webkit -v 4.4.2 -u

When the project is launched on Engine Yard Cloud the cookbooks are automatically run and Jenkins is setup!
