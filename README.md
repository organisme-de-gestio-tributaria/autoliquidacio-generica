# Webservice d'autoliquidacions genèriques per ajuntaments

Cal accedir al webservice mitjançat l'endpoint REST. L’especificació es pot obtenir del [fitxer swagger disponible en aquest repositori](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/swagger%20AutoliquidacioGenerica.json).

El servei es troba a les següents URL's:
* Producció: https://wsreal.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaServiceREST.svc
* Pre-produció (proves): https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaServiceREST.svc

Respecte a la seguretat, cal tenir en compte:
1. L’accés al web service serà via https i amb certificat d'òrgan. 
1. El client ha de suportar el protocol TLS 1.2.
1. Es comprovarà que les autoliquidacions enviades corresponen a l’Ajuntament associat al certificat. No obstant, per les proves es podrà fer servir qualsevol certificat d'òrgan que tingui un CIF associat, per exemple per les proves d'una empresa de software externa a l'Ajuntament.
1. **Previ a les proves cal comunicar el certificat utilitzat a l’ORGT ja que és necessari instal·lar la clau pública als servidors de la ORGT.** Vegeu el procés de sol·licitud a la [pàgina principal](https://github.com/organisme-de-gestio-tributaria/organisme-de-gestio-tributaria)

L’explicació de cada funció del servei i de les dades que demana es troba a:
* [Fitxer swagger disponible en aquest repositori](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/swagger%20AutoliquidacioGenerica.json). 
* [Comentaris del WSDL](https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaService.svc/mex?singleWsdl). Podeu cancel·lar la sol·licitud de certificat a l'accedir al WSDL. Malgrat que aquest wsdl està disponible només per la versió SOAP, els noms dels camps i les explicacions són les mateixes que per la versió REST.
* L’esquema de validació de les dades rebudes es troba en el mateix webservice; en el cas de proves a: https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/schema/AutoliquidacioGenerica.xsd 
* Cal notar que totes les dades de tipus text han d'estar en majúscules.

A grans trets el web service permet:
1. Donar d’alta una autoliquidació, detectant duplicitats (mateix Ajuntament i número de referència de l’autoliquidació), i generant l’abonaré, el codi de barres per pagar-lo al banc, i la URL per fer el pagament a la seu de l'ORGT. També es poden gravar autoliquidacions amb import zero, per exemple en casos d'exempció o benefici fiscal del 100%. El camp NumeroReferenciaAutoliquidacio és opcional, si es deixa a zero el sistema li assigna un número. Si es vol barrejar autoliquidacions amb número assignat pel sistema amb autoliquidacions amb número explícit, es recomana que aquest número explícit sigui de la forma AAAANNNNNN, on AAAA és l'any en curs, i NNNNNN és un número seqüencial dins d'aquest any. Així s'eviten col·lisions entre els números assignats pel sistema i els explícits.
1. Consulta d’una autoliquidació prèviament entrada. L'autoliquidació s'identifica amb:
   -	CodiINE10
   -	Exercici
   -	ConcepteTributari
   -	NumeroReferenciaAutoliquidacio
   - Cal especificar a més el camp DNINIFContribuent, que serveix com a doble comprovació per evitar que al web service de proves, que no té protecció per certificat, un intrús pugui fer proves amb NumeroReferenciaAutoliquidacio correlatius fins encertar alguna autoliquidació i obtenir les seves dades.
1. Generació d’un nou abonaré per una autoliquidació ja entrada, pel cas de què el primer abonaré s’hagi extraviat. Cal aportar les mateixes dades que en el cas de la consulta.



