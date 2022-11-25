---
layout: post
title: "Monitoring Speedtest.net through InfluxDB on OpenWrt"
date: 2022-11-24 09:00:00 -0500
categories: homelab
tags: homelab openwrt speedtest monitoring influxdb
---

# Intro

This Thanksgiving, I was at my Mom’s and wanted to tackle a lightweight monitoring solution for tracking various Speedtest.net metrics. Having recently moved and changed ISPs - she had experienced particular issues in having multiple outages on a daily basis, throughput issues, and the like.

To help with this, the first thing I did was configure a Raspberry Pi Compute Module 4 with the DFRobot IOT Router Board to act as router, gateway, firewall, dns, etc - and just move her consumer Orbi to just Access Point mode. This is a great little board perfect for lightweight routing - Jeff Geerling has a great write up on the board and I would highly recommend replacing your off-the-shelf router with one if you can get your hands on a Compute Module board.

On top of this, I wanted a keep-it-simple-stupid monitoring solution: one that’s lightweight, not full of countless dependencies, as deploying it as my Mom’s, I would prefer a “set and forget” solution rather than one requiring constant maintenance (I’m extremely paranoid to the point of a failed SD card rendering the box non-functional - so I really don’t want a lot running on the box).

InfluxDB Cloud has a great free tier that can maintain logs for 30 days that’s perfect for these small use cases. This is ideal, not having to host my own dedicated InfluxDB stack just for monitoring - I can just scrape and send a few metrics from the box.

# Speedtest CLI Configuration

Speedtest CLI now has precompiled binaries that are statically linked, and can be run on OpenWrt out of the box - so this is much more ideal than running the Python based CLI, which is less performant and requires a whole stack of dependencies. And luckily, it’s compiled for aarch64, so it works perfectly with the Raspberry Pi Compute Module 4 (luckily, because unfortunately Speedtest has some “secret sauce”, and cannot open source the tool for your own compilation).

Installation is straightforward, download the tarball from Speedtest and untar. I just leave it in /root as I persist this directory for Openwrt - but it can also be added to standard /bin directories:

Once you have the binary, run the Speedtest CLI:

First usage of the tool will require you to interactively accept the EULA - after that, the record is stored in your user profile and the CLI can be run without interaction.

To run the tool, I decided to use the .csv output format, because it is extremely easy to parse into InfluxDB, which I will dive into:

# InfluxDB Implementation

Similarly to Speedtest, InfluxDB can also be installed as a single binary. For the Compute Module 4, grab the arm64 binary, and also untar just the same:

Once you have the binary, you need to create an API token, and create a InfluxDB configuration. The easiest way to do this is walked through within the InfluxDB onboarding docs, which will do the work of creating an API token and providing the configuration command like so:

Once you have configured the profile, you can verify the configuration by listing profiles:

To collect data to InfluxDB, we will create a bucket called speedtest to send speedtest data to:

With the bucket created, we can begin sending the csv data to the bucket. InfluxDB can utilize an annotated csv format in order to capture tags, measurements, and datatypes that should be indexed by InfluxDB.

So, we need to figure out how the Speedtest csv data should be annotated to properly index. To get this, I ran the csv output with header information, and mapped each field to the InfluxDB data types as so:

With the annotation complete, the data is ready to send. To run it, I created a simple shell script to capture the annotations, run the speedtest, and send the full output through standard in to the influx command like so:

Finally, to automate the process, I added an hourly cron job to Openwrt to run the shell script, and restarted cron to apply:

After taking some time to let the data populate, I created a simple dashboard to show various speed stats. The result looks like so:

From here, there are many additional enhancements that could be implemented, such as custom alerting upon a certain threshold, or a dead man hook to alert on absence of ingest. For now - I will leave it simply, and just monitor the speed data over time.  I do plan to implement additional monitoring capability here when I get to it - perhaps integrate into my existing LibreNMS stack, Healthchecks.io, or capture some logs to my Grafana Cloud instance. As it stands, I’m pretty satisfied to be able these metrics with just two single binaries and a cron job.

If you want to copy the project, please visit my git repo here:


