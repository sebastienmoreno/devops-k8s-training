test:
	echo "Hit Ctrl-C to stop test"
	echo "Open http://localhost:8000 to see the slides" 
	docker run -v ${PWD}/../slides.md:/public/slides.md -v ${PWD}/../index.html:/public/index.html -v ${PWD}/../static:/public/static -v ${PWD}/../css/custom.css:/public/dist/css/custom.css -v ${PWD}/../css/reset.css:/public/dist/css/reset.css -v ${PWD}/../css/monokai.css:/public/plugin/highlight/monokai.css -p 8000:80 local-revealjs-test:latest

clean:
	docker rmi -f local-revealjs-test:latest

build: clean
	docker build -t local-revealjs-test:latest .
