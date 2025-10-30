# Webservice d'autoliquidacions genèriques per ajuntaments

## Informació tècnica

Cal accedir al webservice mitjançat l'endpoint REST. L’especificació es pot obtenir del [fitxer swagger disponible en aquest repositori](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/swagger%20AutoliquidacioGenerica.json).

El servei es troba a les següents URL's:
* Producció: https://wsreal.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaServiceREST.svc
* Pre-produció (proves): https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaServiceREST.svc

Respecte a la seguretat, cal tenir en compte:
1. L’accés al web service serà via https i amb certificat d'òrgan. 
1. El client ha de suportar el protocol TLS 1.2.
1. Es comprovarà que les autoliquidacions enviades corresponen a l’Ajuntament associat al certificat. No obstant, per les proves es podrà fer servir qualsevol certificat d'òrgan que tingui un CIF associat, per exemple per les proves d'una empresa de software externa a l'Ajuntament.
1. **Previ a les proves cal comunicar el certificat utilitzat a l’ORGT ja que és necessari instal·lar la clau pública als servidors de la ORGT.** Vegeu el procés de sol·licitud a la [pàgina principal](https://github.com/organisme-de-gestio-tributaria/organisme-de-gestio-tributaria)

Els endpoints disponibles són:
1. **AltaAutoliquidacioREST**. Donar d’alta una autoliquidació, detectant duplicitats (mateix Ajuntament i número de referència de l’autoliquidació), i generant l’abonaré, el codi de barres per pagar-lo al banc, i la URL per fer el pagament a la seu de l'ORGT. Cal notar que:
   - La URL de pagament correspon a la passarel·la de pagament de l'ORGT on cal introduir manualment les dades del mitjà de pagament (targeta bancària, compte corrent, etc.). És la mateixa pàgina on aniríem si entréssim a la web de l'ORGT i seleccionéssim "Tràmits i pagaments" i després "Pagament d'impostos i multes" i escollim el pagament amb targeta bancària o Bizum. L'avantatge que té aquesta URL és que ens estalviem posar referència i identificadora de l'abonaré. Cal tenir en compte que el pagament quedarà com a proposta fins que s'aprovi el càrrec de liquidacions corresponent. Quan es fan proves d'integració, es retorna una URL de pagament de la Seu d'Integració de l'ORGT, on només té accés el personal de l'ORGT. En aquest cas, correspon a Tributs Generals i Taxes accedir a aquesta URL per simular el pagament fent servir qualsevol de les targetes de prova, per exemple, les del [BBVA](https://desarrolladores-tpv-virtual-bbva.redsys.es/desarrolladores-inicio/integrate-con-nosotros/tarjetas-y-entornos-de-prueba/).
   - Es poden gravar autoliquidacions amb import zero, per exemple en casos d'exempció o benefici fiscal del 100%.
   - El camp NumeroReferenciaAutoliquidacio és obligatori però un valor de zero fa que el webservice li assigni un número que es retorna amb la resposta.
   - Si es vol barrejar autoliquidacions amb número assignat pel sistema amb autoliquidacions amb número explícit, es recomana que aquest número explícit sigui de la forma AAAANNNNNN, on AAAA és l'any en curs, i NNNNNN és un número seqüencial dins d'aquest any. Així s'eviten col·lisions entre els números assignats pel sistema i els explícits.
   - Disposeu d'una [llista de codis tributaris de l'ORGT](https://github.com/organisme-de-gestio-tributaria/organisme-de-gestio-tributaria/blob/main/Codis%20de%20concepte%20tributari.txt). Aquesta llista pot haver estat ampliada de forma posterior a aquesta publicació.
   - Disposeu de la [descripció i restriccions completes de les dades a enviar aquí](https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaService.svc/mex?singleWsdl) i [a l'esquema de dades XSD](https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/schema/AutoliquidacioGenerica.xsd ).
   - **[Disposeu d'un formulari per a fer crides de prova al endpoint AltaAutoliquidacioREST de proves](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/formulari.html)**. Si heu realitzat el procés de sol·licitud d'adhesió al webservice i utilitzeu aquest formulari amb el certificat compartit amb la ORGT podeu veure el funcionament de l'endpoint de forma senzilla.
1. **ConsultaAutoliquidacioREST**. Consulta d’una autoliquidació prèviament entrada. L'autoliquidació s'identifica amb:
   -	CodiINE10
   -	Exercici
   -	ConcepteTributari. Disposeu d'una [llista de codis tributaris de l'ORGT](https://github.com/organisme-de-gestio-tributaria/organisme-de-gestio-tributaria/blob/main/Codis%20de%20concepte%20tributari.txt). Aquesta llista pot haver estat ampliada de forma posterior a aquesta publicació.
   -	NumeroReferenciaAutoliquidacio
   - Cal especificar a més el camp DNINIFContribuent, que serveix com a doble comprovació per evitar que al web service de proves, que no té protecció per certificat, un intrús pugui fer proves amb NumeroReferenciaAutoliquidacio correlatius fins encertar alguna autoliquidació i obtenir les seves dades.
1. **GenerarAbonareREST**. Generació d’un nou abonaré per una autoliquidació ja entrada, pel cas de què el primer abonaré s’hagi extraviat. Cal aportar les mateixes dades que en el cas de la consulta.

## Exemples de crides i respostes
A continuació es presenten diversos exemples de crides i respostes. Podeu trobar més informació a:
* **[Especificació swagger](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/swagger%20AutoliquidacioGenerica.json). Podeu crear el vostre client de forma automàtica a partir d'aquest fitxer.**
* [Comentaris del WSDL](https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaService.svc/mex?singleWsdl). Podeu cancel·lar la sol·licitud de certificat a l'accedir al WSDL. Malgrat que aquest wsdl està disponible només per la versió SOAP, els noms dels camps i les explicacions són les mateixes que per la versió REST.
* L’esquema de validació de les dades rebudes es troba en el mateix webservice a: https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/schema/AutoliquidacioGenerica.xsd 
* Cal notar que totes les dades de tipus text han d'estar en majúscules.

| Endpoint | Mètode HTTP | Exemples |
|---|---|---|
| AltaAutoliquidacioREST | POST | [URL](https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaServiceREST.svc/AltaAutoliquidacioREST) <br> [Petició](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/Exemples/AltaAutoliquidacioREST%20Peticio.json) <br> [Resposta](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/Exemples/AltaAutoliquidacioREST%20Resposta.json)
| ConsultaAutoliquidacioREST | GET | [URL petició](https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaServiceREST.svc/ConsultaAutoliquidacioREST?CodiINE10=XXXXXXXXXX&Exercici=YYYY&ConcepteTributari=ZZ&NumeroReferenciaAutoliquidacio=AAAAAAAA&DNINIFContribuent=11111111) <br> [Resposta](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/Exemples/ConsultaAutoliquidacioREST%20Resposta.json)
| GenerarAbonareREST | GET | [URL petició](https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaServiceREST.svc/GenerarAbonareREST?CodiINE10=0000000000&Exercici=2023&ConcepteTributari=05&NumeroReferenciaAutoliquidacio=0002300000&DNINIFContribuent=11111111) <br> [Resposta](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/Exemples/GenerarAbonareREST%20Resposta.json)



