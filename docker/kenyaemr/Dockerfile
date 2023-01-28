# Get tomcat and jdk8 images
FROM tomcat:9.0.71-jdk8-corretto-al2

# Make the workdir in tomcat folder
WORKDIR /usr/local/tomcat

# Copy openmrs app into webapps
COPY ./kenyaemr-build/openmrs.war webapps/

# Create Openmrs modules folder
RUN mkdir .OpenMRS; mkdir .OpenMRS/modules

# Copy Openmrs modules files into openmrs folder 
COPY ./kenyaemr-build/modules/ .OpenMRS/modules

# Start the tomcat server through the entrypoint

CMD ["catalina.sh", "run"]
