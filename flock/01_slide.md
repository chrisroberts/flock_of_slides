!SLIDE center
# Flock Of Chefs #

## internode communications

!SLIDE bullets incremental
# Overview #

* Direct node communication
* Customizable APIs
* Resource primatives

!SLIDE bullets incremental
# Tooling #

* DCell (distributed objects)
* Zookeeper (centralized registry)

!SLIDE bullets incremental
# What does it do? #

* Create custom APIs
* Notifications and subscriptions

!SLIDE code small
# Custom APIs 

    @@@ Ruby
    module BenderCookbook
      class HumanApi
        include Celluloid

        def killed_all_humans?
          Humans.destroy!
        end
      end
    end
    BenderCookbook::HumanApi.supervise_as :human_api

!SLIDE code small
# APIs in use

    @@@ Ruby
    file "/opt/human_flag" do
      action :create
      content '0'
      mode 0600
      wait_until do
        FlockOfChefs[:bender].killed_all_humans?
      end
    end

!SLIDE code small
# FlockApi #

    @@@ruby
    module FlockOfChefs
      class FlockApi
        include Celluloid
        def run_chef
          begin
            Thread.new do
              @chef_app.run_chef_client
            end
          end
        end
      end
    end

!SLIDE bullets incremental
# Remote Notifications #

* Available to all resources
* Direct or via search

!SLIDE code small
# Remote subscription

    @@@ Ruby
    file "/opt/test_file" do
      action :nothing
      content 'test'
      remote_subscribes(
        :create, 
        'file[/opt/other_test]', 
        :node => 'node1'
      )
    end

!SLIDE bullets incremental
# Subscription Result

* Registration on node1
* Receives notifications on update

!SLIDE code small
# Remote notification

    @@@ Ruby
    file "/opt/other_test" do
      action :create
      content "other test"
      remote_notifies(
        :create, 
        'file[/opt/test_file]', 
        :node => 'node2'
      )
    end

!SLIDE center
# What does this give us?

!SLIDE bullets incremental
# Orchestration #

* Slightly loaded term
* State - known remote state
* Waiting for state

!SLIDE bullets incremental
# Primatives #

* Provides parts to build tools
* Provides generics not specialties

!SLIDE center
# The dreaded live test!#

!SLIDE center
# Questions/Comments #
