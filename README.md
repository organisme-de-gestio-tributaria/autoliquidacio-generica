# Webservice d'autoliquidacions gen�riques per ajuntaments

Cal accedir al webservice mitjan�at l'endpoint REST. L�especificaci� es pot obtenir del [fitxer swagger disponible en aquest repositori](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/swagger%20AutoliquidacioGenerica.json).

El servei es troba a les seg�ents URL's:
* Producci�: https://wsreal.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaServiceREST.svc
* Pre-produci� (proves): https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaServiceREST.svc

Respecte a la seguretat, cal tenir en compte:
1. L�acc�s al web service ser� via https i amb certificat d'�rgan. 
1. El client ha de suportar el protocol TLS 1.2.
1. Es comprovar� que les autoliquidacions enviades corresponen a l�Ajuntament associat al certificat. No obstant, per les proves es podr� fer servir qualsevol certificat d'�rgan que tingui un CIF associat, per exemple per les proves d'una empresa de software externa a l'Ajuntament.
1. **Previ a les proves cal comunicar el certificat utilitzat a l�ORGT ja que �s necessari instal�lar la clau p�blica als servidors de la ORGT.** Vegeu el proc�s de sol�licitud a la [p�gina principal](https://github.com/organisme-de-gestio-tributaria/organisme-de-gestio-tributaria)

L�explicaci� de cada funci� del servei i de les dades que demana es troba a:
* [Fitxer swagger disponible en aquest repositori](https://github.com/organisme-de-gestio-tributaria/autoliquidacio-generica/blob/main/swagger%20AutoliquidacioGenerica.json). 
* [Comentaris del WSDL](https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/AutoliquidacioGenericaService.svc/mex?singleWsdl). Podeu cancel�lar la sol�licitud de certificat a l'accedir al WSDL. Malgrat que aquest wsdl est� disponible nom�s per la versi� SOAP, els noms dels camps i les explicacions s�n les mateixes que per la versi� REST.
* L�esquema de validaci� de les dades rebudes es troba en el mateix webservice; en el cas de proves a: https://wsproves.orgt.diba.cat/AutoliquidacioGenerica/schema/AutoliquidacioGenerica.xsd 
* Cal notar que totes les dades de tipus text han d'estar en maj�scules.

A grans trets el web service permet:
1. Donar d�alta una autoliquidaci�, detectant duplicitats (mateix Ajuntament i n�mero de refer�ncia de l�autoliquidaci�), i generant l�abonar�, el codi de barres per pagar-lo al banc, i la URL per fer el pagament a la seu de l'ORGT. Tamb� es poden gravar autoliquidacions amb import zero, per exemple en casos d'exempci� o benefici fiscal del 100%. El camp NumeroReferenciaAutoliquidacio �s opcional, si es deixa a zero el sistema li assigna un n�mero. Si es vol barrejar autoliquidacions amb n�mero assignat pel sistema amb autoliquidacions amb n�mero expl�cit, es recomana que aquest n�mero expl�cit sigui de la forma AAAANNNNNN, on AAAA �s l'any en curs, i NNNNNN �s un n�mero seq�encial dins d'aquest any. Aix� s'eviten col�lisions entre els n�meros assignats pel sistema i els expl�cits.
1. Consulta d�una autoliquidaci� pr�viament entrada. L'autoliquidaci� s'identifica amb:
   -	CodiINE10
   -	Exercici
   -	ConcepteTributari
   -	NumeroReferenciaAutoliquidacio
   - Cal especificar a m�s el camp DNINIFContribuent, que serveix com a doble comprovaci� per evitar que al web service de proves, que no t� protecci� per certificat, un intr�s pugui fer proves amb NumeroReferenciaAutoliquidacio correlatius fins encertar alguna autoliquidaci� i obtenir les seves dades.
1. Generaci� d�un nou abonar� per una autoliquidaci� ja entrada, pel cas de qu� el primer abonar� s�hagi extraviat. Cal aportar les mateixes dades que en el cas de la consulta.



