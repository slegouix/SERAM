gpseram.yaml: parts/schema.yaml parts/messages.yaml parts/attributes.yaml
	@echo Validating specification: && swagger-cli validate parts/schema.yaml
	@echo Generating specification: && swagger-cli bundle --type yaml -w 79 -o gpseram.yaml parts/schema.yaml
