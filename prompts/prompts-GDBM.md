# AI4Devs-pipeline

## Prompts

1. Escribe un script YML para GitHub Actions que cumpla con los siguientes criterios:

   1. Pase unos tests de @backend.
   2. Genere un build del @backend.
   3. Despliegue el @backend en una instancia de EC2 de AWS. 
   No escribas codigo aun, dime cuales son los pasos a seguir.

2. Perfecto, genera el script en @pipeline.yml 
   
3. Dame sugerencias para el despliegue del codigo a la instancia EC2. Cual es la mejor forma y la mas segura si las llaves de AWS pueden ir rotando.
   
4. Dame mas informacion sobre la primera opcion "Uso de IAM Roles para EC2" y si se puede usar el identity provider de AWS para que no sea necesario tener las llaves de AWS en el repositorio.
   
5. Como deberia modificar el @pipeline.yml para que la etapa de `deploy_to_ec2` sea con Identity Providers y no utilice la llave SSH.
   > NOTA: Después de interactuar con la IA por un tiempo, me sugirió utilizar los servicios de AWS CodeDeploy y S3. Sin embargo, dado que el uso de CodeDeploy podría generar costos adicionales debido a la interacción con S3 y EC2, decidí descartar esa opción y seguir los pasos de la guía proporcionada por el curso. Se generó el siguiente script:
    ```
      deploy_to_ec2:
        needs: build_backend
        runs-on: ubuntu-latest
        steps:
            - name: Download Artifact
            uses: actions/download-artifact@v2
            with:
                name: built-backend
            - name: Setup AWS credentials
            uses: aws-actions/configure-aws-credentials@v1
            with:
                role-to-assume: arn:aws:iam::123456789012:role/YourDeploymentRole
                aws-region: us-east-1
            - name: Create appspec.yml file
            run: |
                echo 'version: 0.0' > appspec.yml
                echo 'os: linux' >> appspec.yml
                echo 'files:' >> appspec.yml
                echo '  - source: /' >> appspec.yml
                echo '    destination: /path/to/destination' >> appspec.yml
            - name: Deploy to EC2 using AWS CodeDeploy
            run: |
                aws deploy create-deployment \
                --application-name YourApplicationName \
                --deployment-group-name YourDeploymentGroupName \
                --revision revisionType=S3, s3Location={bucket=bucket-name,bundleType=zip,key=built-backend.zip}
                --deployment-config-name CodeDeployDefault.OneAtATime
                --description "Deploying my backend"
                --file-exists-behavior OVERWRITE
    ```

6. Descarta la opcion de utilizar Identity Providers. Como puedo subir el codigo de forma sencilla a la instancia de EC2?.