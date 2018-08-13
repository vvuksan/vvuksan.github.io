---
author: admin
comments: true
date: 2010-07-20 12:30:25+00:00
layout: post
slug: provision-to-cloud-in-5-minutes-using-fog
title: Provision to cloud in 5 minutes using fog
wordpress_id: 281
categories:
- Cloud Computing
- Systems Management
tags:
- EC2
- Ruby
---

Most recently I have been working on disaster recovery project where we are assembling documentation, processes and code to be able to fire up our whole environment in the cloud in case of a major disaster. At Velocity Conference I met [Wesley Beary](http://twitter.com/geemus) who is the main developer for [fog](http://github.com/geemus/fog/), a Ruby cloud computing library. What appealed to me about fog is that it has varying support for different clouds so that we are not stuck using a provider due to our non-portable code. Now off to couple quick example to get you going.

To install fog you will need to install Ruby Gems. If you have them type

    
      sudo gem install fog


The install may fail if you don't have the libxslt and libxml2 dev libraries. On my Ubuntu laptop I resolved it by doing

    
      sudo apt-get install libxslt1-dev libxml2-dev


On Centos/RHEL 5 I had to do

    
       yum install libxslt-devel libxml2-devel


Create a file called config.rb which contains your credentials e.g.

    
    #!/usr/bin/ruby
    
    @aws_access_key_id = "XXXXXXXXXXXXXXXXXX"
    @aws_secret_access_key = "AXXZZZZZZZZZZZZZZZZZZ"
    @aws_region = "us-east-1"


Let's start with the basics. Let's get our currently running instances and what images are available

    
    #!/usr/bin/ruby
    
    require 'rubygems'
    require 'fog'
    
    # Import EC2 credentials e.g. @aws_access_key_id and @aws_access_key_id
    require './config.rb'
    
    # Set up a connection
    connection = Fog::AWS::EC2.new(
        :aws_access_key_id => @aws_access_key_id,
        :aws_secret_access_key => @aws_secret_access_key )
    
    # Get a list of all the running servers/instances
    instance_list = connection.servers.all
    
    num_instances = instance_list.length
    puts "We have " + num_instances.to_s()  + " servers"
    
    # Print out a table of instances with choice columns
    instance_list.table([:id, :flavor_id, :ip_address, :private_ip_address, :image_id ])
    
    ###################################################################
    # Get a list of our images
    ###################################################################
    my_images_raw = connection.describe_images('Owner' => 'self')
    my_images = my_images_raw.body["imagesSet"]
    
    puts "\n###################################################################################"
    puts "Following images are available for deployment"
    puts "\nImage ID\tArch\t\tImage Location"
    
    #  List image ID, architecture and location
    for key in 0...my_images.length
      print my_images[key]["imageId"], "\t" , my_images[key]["architecture"] , "\t\t" , my_images[key]["imageLocation"],  "\n";
    end


Let's spin up a m1.large instance

    
    #!/usr/bin/ruby
    require 'rubygems'
    require 'fog'
    # Import EC2 credentials e.g. @aws_access_key_id and @aws_access_key_id
    require './config.rb'
    
    # Set up a connection
    connection = Fog::AWS::EC2.new(
     :aws_access_key_id => @aws_access_key_id,
     :aws_secret_access_key => @aws_secret_access_key )
    
    server = connection.servers.create(:image_id => 'ami-1234567',
     :flavor_id =>  'm1.large')
    
    # wait for it to be ready to do stuff
    server.wait_for { print "."; ready? }
    
    puts "Public IP Address: #{server.ip_address}"
    puts "Private IP Address: #{server.private_ip_address}"


This may take a while so please be patient.  You could obviously spin up a number of these instances without waiting for any of them to be available then use connection.servers.all to get a list of running instances.

Now let's destroy a running instance

    
    #!/usr/bin/ruby
    require 'rubygems'
    require 'fog'
    # Import EC2 credentials e.g. @aws_access_key_id and @aws_access_key_id
    require './config.rb'
    
    # Set up a connection
    connection = Fog::AWS::EC2.new(
        :aws_access_key_id => @aws_access_key_id,
        :aws_secret_access_key => @aws_secret_access_key )
    
    instance_id = "1-123456"
    
    server = connection.servers.get(instance_id)
    
    puts "Flavor: #{server.flavor_id}"
    puts "Public IP Address: #{server.ip_address}"
    puts "Private IP Address: #{server.private_ip_address}"
    
    server.destroy


There is tons more out there although this gets me going :-). Now off to playing with R.I. Pienaar's [ec2-boot-init.](http://github.com/ripienaar/ec2-boot-init)

Thanks to Wesley Beary for answering questions about fog and Ian Meyer for pointing out [Chef Fog code](http://github.com/opscode/chef/blob/master/chef/lib/chef/knife/ec2_server_create.rb).


#!/usr/bin/ruby

require 'rubygems'
require 'fog'
require 'pp'

# Import EC2 credentials e.g. @aws_access_key_id and @aws_access_key_id
require './config.rb'

# Set up a connection
connection = Fog::AWS::EC2.new(
:aws_access_key_id => @aws_access_key_id,
:aws_secret_access_key => @aws_secret_access_key )

# Get a list of all the running servers/instances
instance_list = connection.servers.all

num_instances = instance_list.length
puts "We have " + num_instances.to_s()  + " servers"

# Print out a table of instances with choice columns
instance_list.table([:id, :flavor_id, :ip_address, :private_ip_address, :image_id ])

###################################################################
# Get a list of our images
###################################################################
my_images_raw = connection.describe_images('Owner' => 'self')

my_images = my_images_raw.body["imagesSet"]

puts "\n###################################################################################"
puts "Following images are available for deployment"
puts "\nImage ID\tArch\t\tImage Location"

for key in 0...my_images.length
print my_images[key]["imageId"], "\t" , my_images[key]["architecture"] , "\t\t" , my_images[key]["imageLocation"],  "\n";
end

###################################################################
# Get a list of all instance flavors
###################################################################
flavors = connection.flavors()

print "\n\n============\nFlavors\n============\n"
#flavors.table([:bits, :cores, :disk, :ram, :name])
flavors.table


