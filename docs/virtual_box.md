## Apache Spark Clusters in VirtualBox with Ananke

### Importing and Updating the VM

If you have received Ananke as a VirtualBox `.ovs` appliance, the first stage is to select the "import appliance" option in VirtualBox.
Before you start using it properly, it's a good idea to update the Docker image it contains. Start the virtual machine, log-in as "ananke"
with password "ananke" and do the following:

![updating the Docker inage](https://raw.githubusercontent.com/kingsBSD/NYC-Green-Cab-Data/master/docs/img/slave_8.png)


```
cd ananke
git checkout master
git pull
```

Now you've pulled the updated code, you can rebuild the Docker image:

```
cd ..
./ananke/build.sh
```

### Getting Data into the VM

You can start the container by simply typing `./start`, but if you want to have persistant data in the virtual machine that persists after
the Docker container terminates, do the following:

```
mkdir data
./ananke/run-data.sh /home/ananke/data
```

You can now visit `http;//localhost:5000` and start Jupyter on PySpark on a single Spark node. Go to the Jupyter notebook and start uploading files:

![Uploading data into Jupyter](https://raw.githubusercontent.com/kingsBSD/NYC-Green-Cab-Data/master/docs/img/ananke_08.png)

It's now safe to stop the Docker container with `control-C`. Type `ls data/`, and you'll see everything you uploaded is still there.
If you restart the container with `./ananke/run-data.sh /home/ananke/data` and restart Jupyter, all the files will be where you left them.
There is no way to do this when using the Hadoop File system (HDFS) yet.
You can start a new Python3 kernel in Jupyter and play with the data, but it's more fun to get a cluster going.

### Making a Spark Cluster in VirtualBox

#### Host Only Networking

Up to now, we've been using VirtualBox's "Network Address Tranlsation" mode. To get a virtual machine talking to others on the same
wireless network, we could use "Bridged Networking", but Eduroam doesn't support this. The best was to get a cluster going on multiple
machines is to learn how to use Docker and launch the container outside of VirtualBox. For now, you can get multiple virtual machines
on your laptop talking to each other. This will require using "Host Only" networking.

Go to the "preferences" option in VirtualBox, select "Network", then the "Host-only Networks" tab, and create a host-only network.

![host-only network](https://raw.githubusercontent.com/kingsBSD/NYC-Green-Cab-Data/master/docs/img/slave_10.png)

Now find the settings for your virtual machine. Under the "Network" option, select the host-only adapter you created.

![add host adapter](https://raw.githubusercontent.com/kingsBSD/NYC-Green-Cab-Data/master/docs/img/slave_12.png)

#### Cloning the Virtual Machine

Now your VM needs another one to talk to. Select the "clone" option from the "Machine" menu:

![Cloning a VM](https://raw.githubusercontent.com/kingsBSD/NYC-Green-Cab-Data/master/docs/img/slave_13.png)

Start both the machines and log-in. "localhost" won't point to "localhost" anymore, type `ifcongig` to find out the machine's
new IP address:

![ifconfig](https://raw.githubusercontent.com/kingsBSD/NYC-Green-Cab-Data/master/docs/img/slave_14.png)

In this case, the IP address is `192.168.56.102`. Restart the Docker container with `./ananke/run-data.sh /home/ananke/data`.
The web-app would be at `http://192.168.56.102`. Do the same for the other virtual machine.





### All Done!

It should now be possible to start a cluster on one virtual machine, and give its IP address to another in order to
join it. You can then start a Jupyter notebook server via PySpark, and start playing with the data.

![Spark cluster](https://raw.githubusercontent.com/kingsBSD/NYC-Green-Cab-Data/master/docs/img/slave_1.png)


