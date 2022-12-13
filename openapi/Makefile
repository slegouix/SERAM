gpseram_v2.yaml: gpseram.yaml messages.yaml attributes.yaml
	@echo Validating specification: && swagger-cli validate gpseram.yaml
	@echo Generating specification: && swagger-cli bundle --type yaml -w 79 -o gpseram_v2.yaml gpseram.yaml
