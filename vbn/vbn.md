# OTP setup for VBN
There is not just one way to do it.
That is why you have the following selection.

## Prepare host system
* Set up Java
 * Download [JRE](https://adoptopenjdk.net/?variant=openjdk11&jvmVariant=hotspot)
 * Copy JRE onto host system like this:
  * ```scp -P 22 ~/OpenJDK11U-jre_x64_linux_hotspot_11.0.12_7.tar.gz sib@83.223.94.182:/home/sib/```
 * Move and extract archive like this:
  * ```sudo mv ~/OpenJDK11U-jre_x64_linux_hotspot_11.0.12_7.tar.gz /opt/```
  * ```sudo tar -xzf OpenJDK11U-jre_x64_linux_hotspot_11.0.12_7.tar.gz```
 * At JAVA_HOME to ```~/.bashrc``` like this:
  * ```export JAVA_HOME=/opt/jdk-11.0.12+7-jre```
 * Extend PATH in ```~/.bashrc``` like this:
  * ```export PATH=$PATH:$JAVA_HOME/bin```
 * Test JAVA version like this:
  * ```java -version```

## Setup according to [basic tutorial](http://docs.opentripplanner.org/en/latest/Basic-Tutorial/)
* Download GTFS [data](vbn.gtfs.zip) from Connect-Fahrplanauskunft GmbH
* Download osm map data from [geofabrik](lower-saxony.osm.pbf.ln)
* Get bounding box for VBN from https://boundingbox.klokantech.com/
 * csv:7.6966,52.4508,9.6501,53.8503
* Extract VBN map data using the tool `osmconvert` creating the [file](vbn.osm.pbf)
 * `cd ~/vbn/otp/`
 * `osmconvert lower-saxony.osm.pbf.ln -b=7.6966,52.4508,9.6501,53.8503 --complete-ways -o=vbn.osm.pbf`
 * The output file is called `vbn.osm.pbf`
* Build a street graph with OSM and elevation data only (ignoring transit input files)
 * `java -Xmx5G -jar otp.jar --buildStreet .`
 * The output file is called `streetGraph.obj`
* Build a graph layering transit data on top of the saved street graph (built using the previous command)
 * `java -Xmx5G -jar otp.jar --loadStreet --save .`
 * The output file is called `graph.obj`
* Copy config files to the right place like this:
 * ```tbd```
* Finally, the server can be started using the --load parameter
 * java -Xmx5G -jar otp.jar --load .

## Setup accoriding to [cookbook](https://transportkollektiv.github.io/digitransit-setup/index.html)
There is a dedicated [repository[(https://github.com/transportkollektiv/digitransit-setup) for this cookbook.

to be continued

## Setup accoding to [Stadtnavi tutorial](https://github.com/stadtnavi/stadtnavi-tutorial)

### Lessons Learned
Setting both `MEMORY` and `GRAPH_BUILD_MEMORY` to 4 GB and running the instruction ```docker-compose --env-file=.env.vbn up --build -d``` on a computer with 8 GB total and 4 GB memory available results in the following error.

```
Exception in thread "main" java.lang.OutOfMemoryError: Java heap space
	at org.opentripplanner.common.geometry.PackedCoordinateSequence$Double.<init>(PackedCoordinateSequence.java:246)
	at org.opentripplanner.common.geometry.Serializable2DPackedCoordinateSequenceFactory.create(Serializable2DPackedCoordinateSequenceFactory.java:15)
	at org.locationtech.jts.geom.GeometryFactory.createLineString(GeometryFactory.java:573)
	at org.opentripplanner.common.geometry.GeometryUtils.makeLineString(GeometryUtils.java:53)
	at org.opentripplanner.common.geometry.GeometryUtils.addStartEndCoordinatesToLineString(GeometryUtils.java:71)
	at org.opentripplanner.common.geometry.CompactLineString.compactLineString(CompactLineString.java:110)
	at org.opentripplanner.model.TripPattern.setHopGeometry(TripPattern.java:163)
	at org.opentripplanner.model.TripPattern.setHopGeometries(TripPattern.java:158)
	at org.opentripplanner.graph_builder.module.geometry.GeometryAndBlockProcessor.run(GeometryAndBlockProcessor.java:161)
	at org.opentripplanner.graph_builder.module.GtfsModule.buildGraph(GtfsModule.java:153)
	at org.opentripplanner.graph_builder.GraphBuilder.run(GraphBuilder.java:81)
	at org.opentripplanner.standalone.OTPMain.startOTPServer(OTPMain.java:136)
	at org.opentripplanner.standalone.OTPMain.main(OTPMain.java:52)
12:11:10.329 INFO (OtpStartupInfo.java:47) OTP SHUTTING DOWN (version: 2.1.0-SNAPSHOT, ser.ver.id: 8, commit: f4d04d59568708cf9778b48f8cb8bbcb9d16b269, branch: dev-2.x)
The command '/bin/sh -c java -Xmx$MEMORY -jar otp-shaded.jar --build --save /opt/opentripplanner/build/' returned a non-zero code: 1
```

to be continued