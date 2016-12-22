README.md  

Erstelle Docker Image:  

	docker build -t gollum .

Starten des Docker images: 

	docker run -v `pwd`:/wiki -p 4567:80 gollum

Dann Aufruf im Browser:  

	http://localhost:4567

und los geht es mit der Dokumentation.

