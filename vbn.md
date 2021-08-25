# OTP setup for VBN
* Download GTFS [data](vbn.gtfs.zip) from connect Fahrplanauskunft GmbH
* Download osm map data from [geofabrik](lower-saxony.osm.pbf.ln)
* Get bounding box for VBN from https://boundingbox.klokantech.com/
 * csv:7.6966,52.4508,9.6501,53.8503
* Extract VBN map data using the tool `osmconvert` creating the [file](vbn.pbf)
 * `cd ~/vbn/otp/`
 * `osmconvert ???.osm.pbf.ln -b=??? --complete-ways -o=vbn.pbf`
 * The output file is called `vbn.pbf`
* Build a street graph with OSM and elevation data only (ignoring transit input files)
 * `java -Xmx2G -jar otp.jar --buildStreet .`
 * The output file is called `streetGraph.obj`
* Build a graph layering transit data on top of the saved street graph (built using the previous command)
 * `java -Xmx2G -jar otp.jar --loadStreet --save .`
 * The output file is called `graph.obj`
* Finally, the server can be started using the --load parameter
 * java -Xmx2G -jar otp.jar --load .
