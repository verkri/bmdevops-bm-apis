# Kozos API Repository

A celja ennek a repo-nak, hogy minden app-to-app es service-to-service (FE<->BE, BE<->BE illetve Bankmonitor BE <-> Kulso rendszer) kozotti 

 - OpenAPI leirokat (yml) tarolja, 
 - az api leirobol *maven package*-t generaljunk (mind kliens, mind szerver stubok)
 - az api leirobol *npm package*-t generaljjunk (TS kliens)

A repository strukturaja olyan, hogy minden API-t es a hozza tartozo generatorokat kulon mappaba teszunk:

 - `bm_bankportal`
 - `bm_offergenerator`
 - `bm_crm`
 - `otp_mortgage`
 - `cib_mortgage`
 - `...`

## NPM es Maven package-k

Minden egyes API-hoz AWS CodeArtifact-ben keszitunk 1-1 NPM es Maven package-t. Az osszes package egy CodeArtifact repository alatt talalhato: `bm-apis`.