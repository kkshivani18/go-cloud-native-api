build:
	@GOOS=linux GOARCH=amd64 go build -o bootstrap
	@build-lambda-zip -o function.zip bootstrap