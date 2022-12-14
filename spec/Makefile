gpseram.yaml: parts/schema.yaml parts/messages.yaml parts/attributes.yaml
	@echo -n "Validating specification: "
	@swagger-cli validate parts/schema.yaml
	@echo -n "Generating specification: "
	@#swagger-cli bundle --type yaml -w 79 -o gpseram.yaml parts/schema.yaml
	@cat parts/schema.yaml parts/messages.yaml parts/attributes.yaml | sed -s 's:\(messages\|attributes\).yaml#/:#/components/schemas/:g' > gpseram.yaml && echo  "gpseram.yaml generated" || echo FAIL
