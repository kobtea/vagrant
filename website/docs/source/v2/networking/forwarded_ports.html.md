---
page_title: "Forwarded Ports - Networking"
sidebar_current: "networking-fp"
---

# Forwarded Ports

**Network identifier: `forwarded_port`**

Forwarded ports allow you to access a port on your host machine and have
all data forwarded to a port on the guest machine, over either TCP or UDP.

For example: If the guest machine is running a web server listening on port 80,
you can make a forwarded port mapping to port 8080 (or anything) on your host
machine. You can then open your browser to `localhost:8080` and browse the
website, while all actual network data is being sent to the guest.

## Defining a Forwarded Port

The forwarded port configuration expects two parameters, the port on the
guest and the port on the host. Example:

```ruby
Vagrant.configure("2") do |config|
  config.vm.network "forwarded_port", guest: 80, host: 8080
end
```

This will allow acessing port 80 on the guest via port 8080 on the host.

## Forwarded Port Protocols

By default, any defined port will only forward the TCP protocol. As an optional
third parameter, you may specify `protocol: 'udp'` in order to pass UDP 
traffic. If a given port needs to be able to listen to the same port on both 
protocols, you must define the port twice with each protocol specified, like 
so:

```
Vagrant.configure("2") do |config|
  config.vm.network "forwarded_port", guest: 2003, host: 12003, protocol: 'tcp'
  config.vm.network "forwarded_port", guest: 2003, host: 12003, protocol: 'udp'
end
```

## Port Collisions and Correction

It is common when running multiple Vagrant machines to unknowingly create
forwarded port definitions that collide with each other (two separate
Vagrant projects forwarded to port 8080, for example). Vagrant includes
built-in mechanism to detect this and correct it, automatically.

Port collision detection is always done. Vagrant will not allow you to
define a forwarded port where the port on the host appears to be accepting
traffic or connections.

Port collision auto-correction must be manually enabled for each forwarded
port, since it is often surprising when it occurs and can lead the Vagrant
user to think that the port wasn't properly forwarded. Enabling auto correct
is easy:

```
Vagrant.configure("2") do |config|
  config.vm.network "forwarded_port", guest: 80, host: 8080,
    auto_correct: true
end
```

The final `:auto_correct` parameter set to true tells Vagrant to auto
correct any collisions. During a `vagrant up` or `vagrant reload`, Vagrant
will output information about any collisions detections and auto corrections
made, so you can take notice and act accordingly.
