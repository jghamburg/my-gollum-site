README.md  

Create basic Docker Image:  

	docker build -t gollum .

Start the Docker images: 

	docker run -v `pwd`:/wiki -p 4567:80 gollum

Call Gollum from browser:  

	http://localhost:4567

and now start to document ...  
.. no more excuses ;-)  

