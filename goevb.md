# OTP setup for GOEVB
* Download GTFS [data](goevb.gtfs.zip) from VBN
* Download osm map data for lower saxony from [geofabrik](lower-saxony.osm.pbf.ln)
* Get bounding box for GOEVB from https://boundingbox.klokantech.com/
 * csv: 9.800175,51.489895,10.050114,51.600227
* Extract GOEVB map data from lower saxony map data using the tool `osmconvert` creating the [file](goevb.pbf)
 * `cd ~/goevb/otp/`
 * `osmconvert lower-saxony.osm.pbf.ln -b=9.800175,51.489895,10.050114,51.600227 --complete-ways -o=goevb.pbf`
 * The output file is called `goevb.pbf`
* Build a street graph with OSM and elevation data only (ignoring transit input files)
 * `java -Xmx1G -jar otp.jar --buildStreet .`
 * The output file is called `streetGraph.obj`
* Build a graph layering transit data on top of the saved street graph (built using the previous command)
 * `java -Xmx1G -jar otp.jar --loadStreet --save .`
 * The output file is called `graph.obj`
* Finally, the server can be started using the --load parameter
 * java -Xmx1G -jar otp.jar --load .
