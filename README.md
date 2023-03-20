# RestAPI-Java
 1. how to read from body.json and replace 
>
 ````
 public void setBody(String jsonFileName, Map<String, String> bodyData) {
		String temp = "";
		Map<String, String> newFieldMap = new HashMap<>();
		try {
			Iterator<Entry<String, String>> it = bodyData.entrySet().iterator();
			temp = ReusableMethods.ReadPayloadFromJsonFile("src/test/resources/payloads/" + jsonFileName + ".json");
			if (System.getProperty("mappingId") != null) {
				if (!System.getProperty("mappingId").isEmpty())
				log.debug("MappingId returned from the context:" + String.valueOf(System.getProperty("mappingId")));
			}
			while (it.hasNext()) {
			Map.Entry<String, String> pair = (Map.Entry<String, String>) it.next();
			if (temp.contains("%" + pair.getKey())) {
						temp = temp.replaceAll("\"%" + pair.getKey() + "\"", "\"" + pair.getValue() + "\"");
				} else {
						newFieldMap.put(pair.getKey(), pair.getValue());
				}
			}
			JsonObject jsonBody = new JsonParser().parse(temp).getAsJsonObject();
				// add new fields from newFieldMap
			for (String key : newFieldMap.keySet()) {
					jsonBody.addProperty(key, newFieldMap.get(key));
					//jsonBody.addProperty(key, newFieldMap.get(key));
			}`

			this.bodyMap = (Map<String, Object>) testContext.scenarioContext.getContext(Context.requestBodyMap);
			if (bodyMap != null) {
				for (String parentNode : bodyMap.keySet()) {
					if (bodyMap.get(parentNode) instanceof JsonArray) {
							jsonBody.add(parentNode, (JsonArray) bodyMap.get(parentNode));
					} else if (bodyMap.get(parentNode) instanceof String) {
							jsonBody.addProperty(parentNode, bodyMap.get(parentNode).toString());
					} else {
							JsonObject attrs = ((JsonObject) bodyMap.get(parentNode));
							for (String attr : attrs.keySet() ){
								jsonBody.get(parentNode).getAsJsonObject().add(attr, attrs.get(attr));
						}
					}
				}
			}
				requestSpecs.body(jsonBody.toString());
		} catch (Exception e) {
			step.write(e.getLocalizedMessage());
			e.printStackTrace();
		}
	}
