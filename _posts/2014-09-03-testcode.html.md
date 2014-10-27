---
layout: post
title: Test code highlight
date: 2014-08-06
draft: true
tags: [book]
---

Testing some code
==================


some javascript code



```javascript

var options = {
  title: ["Rasi", "14/04/2014 07:00AM", "Erode, Tamil Nadu, India"],
  width: 540,
  height: 400,
  styleSheet: "http://rawgit.com/erajasekar/astrochartjs/master/dist/themes/default.css"
};

var chartData = { 1: ["Su", "Ke"],3: ["Ju"], 6: ["Ma", "Asc"], 7: ["Mo", "Sa", "Ra"], 11: ["Ve"], 12: ["Me"]};

var astroChart = new AstroChart("#chart2");
astroChart.draw(chartData, options);

```

done with code

some xml

```xml

<dependency>
  <groupId>com.salesforce.mandm</groupId>
  <artifactId>inventorydb-restclient</artifactId>
  <version>0.4-SNAPSHOT</version>
</dependency>
<dependency>
  <groupId>com.salesforce.mandm</groupId>
  <artifactId>jsonpath-databinding</artifactId>
  <version>0.1-SNAPSHOT</version>
</dependency>
<dependency>
  <groupId>com.salesforce.mandm</groupId>
  <artifactId>inventorydb-adapter</artifactId>
  <version>0.3-SNAPSHOT</version>
</dependency>

```

then some java

```java

Query query = select("name")
                   .from(source(IDB.CLUSTERS).datacenter(var("dc")))
                   .where(condition("operationalStatus").eq("ACTIVE")
                       .and("dr").eq("false")
                       .and("clusterType").eq("POD"))
                   .build();
Map<String,String> queryVariables = new HashMap<String, String>();
String datacenters[] = new String[] {"asg", "sjl"};

for(String datacenter : datacenters){
   queryVariables.put("dc", datacenter);
   JsonNode actual = DataProviderFactory
                        .createRequestSigningRestDataProvider(iDBConfiguration, requestSignerConfiguration)
                        .query(query, JsonNode.class, queryVariables);
}

```