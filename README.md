Applikasjon og basis pipeline:
https://github.com/anonexam2018/exam-infra
https://github.com/anonexam2018/exam-app

Oppgaver:
Applikasjon og basis pipeline
DevOps Teori


for å kjøre oppgaven må du skrive i infra:

fly -t <target-navn> set-pipeline -c concourse/pipeline.yml -l credentials.yml -p <pipeline-navn>

Filer som må endres:
/infra

credentials.yml:

Her må du endre github_token til din egen Personal Access Token, samt deploy_key_infra, deploy_key_app, heroku_email, heroku_api_key, heroku_app_name og statuscake_api_key. Her må du sette inn dine egne deploy keys, og heroku informasjon. Statuscake krever egen api key og bruker.

.gitignore:

her må du legge til 
.terraform
*.tfstate.backup

for å forhindre at terraform ødelegger concourse-rutinen din.

/infra/terraform:

provider_heroku.tf:

her må du endre "email" til din egen.

variables.tf:

her må du endre verdien i "default" på både app_prefix og pipeline_name,
ikke endre navnene på "app_prefix" eller "pipeline_name", da det kan forplante seg som en feil i resten av kjøringen.

statuscake.tf:

her må du endre username til din egen.

/infra/concourse
pipeline.yml:

her må det endres i name: samt i get:, file: og input_mapping:

du må også endre "uri" i resources: til din egen.

Kjøring hvert push:

for at oppgaven skal kjøre hver gang du gjør en push, må du legge inn "trigger: true" under aggregate: get:, rett over -task: i begge jobbene.

Når du trigger et build med dette i filen, starter concourse to builds samtidig, og kjører først en feilfri build, etterfulgt av et build som feiler, fordi den ønsker å pushe igjen. Dette gjør at du må kjøre git pull, og så git push igjen.

Grunnen til at jeg ikke har med dette i oppgaven min er at det til slutt startet en ny jobb som detachet HEAD av git'en min, og kræsjet programmet ved at den triggeret to builds i concourse. Dette fungerte fint frem til helt til slutten av oppgaven. Hvis det er problemer med det er det bare å fjerne det, men det er min løsning på CI. 

/infra/concourse/java
task.yml:

her må du endre inputs: name: til mappen din (ikke source, men den etter) og run: path: til pathen til filen din.

Inne i exam-app har jeg kun lagt til en enkel "assert true" for å sjekke at en test kan kjøres (slik vi har gjort i forelesning)

Metrics:

Du kan få metrics på statuscake om du har en bruker der, og legger inn din egen API key.

DevOps Teori:

III. Config

Config betyr for meg som utvikler at jeg ikke lagrer passord eller nøkler i filer, men heller i terminalen / hemmelige filer, og dette lar meg utvikle applikasjoner uten å bekymre meg for at disse hemmelighetene skal bli røpet til andre, eller at jeg ikke skal ha mulighet til å lagre de, og så måtte skrive de ned. Dette er et viktig prinsipp å holde, fordi det kan bli slitsomt å ikke ha nøkler og passord skrevet ned på et trygt sted.

X. Dev/prod parity

Dev / prod parity betyr for meg som utvikler at jeg ikke skal utvikle en og en gren av min applikasjon, men heller at de utvikles samtidig og slippes så straks de er "klare". Endringer i applikasjonen lager en trigger som gjør at resten av applikasjonen gradvis endrer seg. Dette gjør at development og produksjon blir nærmere koblet. Dette er viktig for å holde utviklingtiden lav, og produksjonstiden nesten like lav. Dette skaper en flyt som gjør applikasjoner raskere å levere. 

XI. Logs

Logs betyr for meg som utvikler at det er en feedback på hva som skjer i prosessen, uten at jeg må lete i filer etter nøyaktig hva jeg ser etter. Om jeg ser i konsollen hva som skjer, så har jeg en raskere forståelse av hva som må fikses om det er en feil. Å logge det i konsollen gjør det også tryggere, ettersom at feil ikke lagres og kan utnyttes. Det skal gjerne være korte meldinger slik at det ikke er en fil på hundretusen linjer du må lete gjennom for å finne feilen.


kilder:

https://www.terraform.io/docs/providers/heroku/r/app.html
https://devcenter.heroku.com/articles/container-registry-and-runtime
https://www.baeldung.com/dropwizard-metrics
https://concoursetutorial.com/miscellaneous/run-tests-before-deploy/
https://devcenter.heroku.com/articles/build-docker-images-heroku-yml
https://concoursetutorial.com/miscellaneous/docker-images/
https://stackoverflow.com/questions/45141402/dockerfilebuild-and-run
