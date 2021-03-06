# Report for the Devops exercise

# Steps

1.   Installed docker engine and docker compose with `apt-get` following instructions at https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository. I also added $USER to the docker group.

2.   Clone repo https://github.com/deviantony/docker-elk and switch to the branch `tls`

     ```bash
     $ git clone https://github.com/deviantony/docker-elk.git
     $ cd docker-elk
     $ git checkout -t origin/tls
     ```
     
     Note that during the exercise I cloned the original repository and moved to the tls branch, which have not been proagated to my fork.

3.   Build and run the ELK stack

     ```
     $ docker compose up -d
     ```
     
     To check the status containers to see if all are running:
     
     ```bash
     $ cd ~/docker-elk
     $ docker-compose ps
     ```
     
4.   Tests

      4.1  On the server

          ```bash
          $ curl https://es.test4.thehip.app:9200 -u elastic:changeme -k
          ```

          Output:
          ```output
          {
            "name" : "06b98858c7ee",
            "cluster_name" : "docker-cluster",
            "cluster_uuid" : "kZCdORVURAewvwzot9vdcw",
            "version" : {
              "number" : "8.2.3",
              "build_flavor" : "default",
              "build_type" : "docker",
              "build_hash" : "9905bfb62a3f0b044948376b4f607f70a8a151b4",
              "build_date" : "2022-06-08T22:21:36.455508792Z",
              "build_snapshot" : false,
              "lucene_version" : "9.1.0",
              "minimum_wire_compatibility_version" : "7.17.0",
              "minimum_index_compatibility_version" : "7.0.0"
            },
            "tagline" : "You Know, for Search"
          }
          ```

      4.2  From my machine
      
      I opened https://es.test4.thehip.app:9200 in my browser and enter the elastic credentials (user: elastic, pwd: changeme)

5.   Configure to start the stack at login by creating a `~/.bashrc` with the following content:

     ```bash
     # Launch the ELK stack at startup
     cd docker-elk/
     docker-compose up -d
     cd ~
     ```
     
This is all I managed to do within the 4 hours. I was not familiar with the elasticsearch and ELK stack and it took me some time to find a solution for the TLS encryption. I am sorry but I did not have time to ingest a log with logstash and display it with Elasticsearch. Also later, I realized that my connection to Kibana was also not secured.

## Note

I could not let this project uncompleted, and so I decided to keep working on this using my local setup. :grin:

A fully working solution is accessible on the `tls` branch of this repo: https://github.com/sebastientourbier/docker-elk/tree/tls

I created the following PR to easily track the different changes I made to allow TLS encryption for Elasticsearch and Kinaba, as well as the data ingestion with Logstash: https://github.com/sebastientourbier/docker-elk/pull/1/files

    
