#########################
CARMA Ouster Lidar Driver
#########################

This is a fork of the `Ouster Example Code <https://github.com/ouster-lidar/ouster_example>`_ that is used for connecting to and configuring Ouster sensors, reading and visualizing data, and interfacing with ROS. This fork has been modified to allow for building a Docker image that can serve as a lidar driver for the `CARMA Platform <https://github.com/usdot-fhwa-stol/carma-platform>`_.

Ubuntu 20.04 Installation
-------------------------
Assuming the CARMA Platform is installed at ``~/carma_ws/src``::

    cd ~/carma_ws/src
    git clone https://github.com/VT-ASIM-LAB/ouster_lidar_driver.git
    cd ouster_lidar_driver/docker
    sudo ./build-image.sh -d

After the Docker image is successfully built, add the following lines to the appropriate ``docker-compose.yml`` file in the ``carma-config`` directory::

    ouster-lidar-driver:
      image: usdotfhwastoldev/carma-ouster-lidar-driver:develop
      container_name: ouster-lidar-driver
      network_mode: host
      volumes_from:
        - container:carma-config:ro
      environment:
        - ROS_IP=127.0.0.1
      volumes:
        - /opt/carma/logs:/opt/carma/logs
        - /opt/carma/.ros:/home/carma/.ros
        - /opt/carma/vehicle/calibration:/opt/carma/vehicle/calibration
      command: bash -c '. ./devel/setup.bash && export ROS_NAMESPACE=$${CARMA_INTR_NS} && wait-for-it.sh localhost:11311 -- roslaunch /opt/carma/vehicle/config/drivers.launch drivers:=ouster_lidar'

Finally, add the following lines to the ``drivers.launch`` file in the same directory as ``docker-compose.yml``::

    <include if="$(arg ouster_lidar)" file="$(find ouster_ros)/ouster.launch">
      <arg name="sensor_hostname" value="10.5.5.53"/>
      <arg name="udp_dest" value="10.5.5.1"/>
      <arg name="metadata" value="/opt/carma/vehicle/calibration/ouster/OS1-64U.json"/>
    </include>

``10.5.5.53`` should be replaced with the IP address of your lidar sensor. See `this guide <https://github.com/SteveMacenski/ouster_ros1>`_ for more details.

ROS API (stable)
----------------

ouster_ros
^^^^^^^^^^

Nodes
"""""
* ``os_node``
* ``os_cloud_node``

Topics
""""""
* `` ``: .
* `` ``: .
* ``os_cloud_node/driver_discovery``: publishes the CARMA `DriverStatus <https://github.com/usdot-fhwa-stol/carma-msgs/blob/develop/cav_msgs/msg/DriverStatus.msg>`_ message.

Services
""""""""
* `` ``: .

Parameters
""""""""""
* `` ``: .

Examples
--------

See the ``ouster.launch`` file in the ``ouster_ros`` directory.
