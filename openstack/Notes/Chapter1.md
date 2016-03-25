# Chapter 1: Basic Knowledge

### Tips:
  1. what does hardware supply?
  2. what does Protocol Layer supply?
  3. what does Virtualize Layer supply?
  4. what does OS Layer supply?
  5. what does Application Layer supply?

### Create instance from dashboard:
  1. Horizon gets the Nova's url and token from keystone
  2. Horizon sends a RESTful API request to Nova to create a VM with the token
  3. nova-compute request to download the image from glance-api
  4. Glance get the image file from swift(object storage)
  4. Get the network inforamtion and decide the network mode and create connection through Neutron
  5. nova-compute start the VM

### Create Volume from dashboard:
  1. Horizon gets the Cinder's url and token from keystone
  2. Horizon sends a RESTful API request to Cinder to create a volume with the token
  3. Horizon sends a RESTful API request to Nova to attach the volume
  4. nova-compute sends RPC command to Cinder to attach the volume


### Tempest introduction
Goal: Used to function test and integration test
Framework: unittest2 and nose
Style: pyunit with Library: testtools and testresources
Test Cases:  API, CLI, Complex scenario, Stress test, The third-party API, Whitebox, Services, Common.