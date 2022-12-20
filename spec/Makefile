gpseram.yaml: parts/binding.yaml parts/messages.yaml parts/attributes.yaml
	@echo -n "Validating specification: "
	@swagger-cli validate parts/binding.yaml
	@echo -n "Generating specification: "
	@cat parts/binding.yaml parts/messages.yaml parts/attributes.yaml | sed -s 's:\(messages\|attributes\).yaml#/:#/components/bindings/:g' > gpseram.yaml && echo  "gpseram.yaml generated" || echo FAIL

