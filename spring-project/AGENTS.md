# Fejlesztési és Architektúra Szabályok (Spring Boot 4 & Java 25)

## Technológiai Stack & Verziók

- **Nyelv:** Java 25 (LTS)
- **Keretrendszer:** Spring Boot 4.0 (Spring Framework 7.0)
- **Perzisztencia:** Jakarta Persistence / Hibernate ORM 7.0
- **API Szabvány:** Jakarta EE 11
- **Build Eszköz:** Maven 3.9+

## Modern Java 25 Nyelvi Funkciók

- **Modern Szintaxis:** Előnyben kell részesíteni a Java 25-ig bevezetett modern nyelvi elemeket (pl. Records, Pattern Matching, Sealed Classes, Sequenced Collections).
- **Null-biztonság:** Szigorúan kerülni kell a nyers `null` értékek visszaadását. A szerviz rétegben a hiányzó értékek jelzésére kötelező az `Optional<T>` használata.
- **Konkurenciakezelés:** Blokkoló I/O műveletek esetén hagyatkozz a virtuális szálakra (Virtual Threads: `spring.threads.virtual.enabled=true`). Kerüld a manuális szálkezelést (thread pool management), kivéve ha kifejezetten indokolt.

## Architektúra és Rétegek

- **Csomag névterek (Namespaces):** Kizárólag a `jakarta.*` importokat használd a régi, elavult `javax.*` helyett.
- **Tiszta Architektúra (Clean / Layered Architecture):** Kövesd a szigorú rétegelt felépítést:
  - `controller`: REST végpontok, validáció, DTO leképezés.
  - `service`: Üzleti logika, tranzakciós határok (`@Transactional`).
  - `repository`: Spring Data JPA interfészek.
- **Adatmodell Elkülönítés:** A DTO (Data Transfer Object) osztályok legyenek teljesen leválasztva az adatbázis entitásokról. **Soha ne engedd ki a JPA entitásokat közvetlenül a REST kontrollereken keresztül!**
- **DTO Leképezés:** A DTO-k és entitások közötti konverzióra használj MapStruct-ot.

# Mikroszerviz és API Tervezési Szabályok

## Mikroszerviz Alapelvek

- **Állapotmentesség (Statelessness):** Minden mikroszerviznek szigorúan állapotmentesnek (stateless) kell lennie a horizontális skálázhatóság biztosítása érdekében.
- **Konfigurációkezelés:** Használj típusbiztos konfigurációs tulajdonságokat (`@ConfigurationProperties`) a mereven kódolt `@Value` annotációk helyett.

## Megfigyelhetőség (Observability)

- **Metrikák és Nyomkövetés:** Minden kimenő HTTP/gRPC kliens esetében biztosítani kell a megfelelő Micrometer metrikákat és a folyamat-azonosítók (distributed tracing headers) terjesztését.

## API Szabványok és Hibakezelés

- **Validáció:** A bejövő kéréseket a `jakarta.validation-api` annotációival (`@Valid`, `@NotNull`, `@Size`, stb.) kell ellenőrizni a kontroller rétegben.
- **Központosított Hibakezelés:** A kivételek kezelését globálisan, egy `@RestControllerAdvice` osztályban kell megvalósítani.
- **Hibaformátum:** A hibaüzeneteknek az RFC 7807 szabványnak megfelelő `ProblemDetail` objektumokat kell visszaadniuk.

# Junie AI Kontextus - Spring Boot 4 & Java 25

Ha kódot generálsz vagy módosítasz, mindig vedd figyelembe:

1. Csak Java 25 szintaxist használj (Records, Pattern Matching).
2. Nincs `javax.*`, csak `jakarta.*`.
3. Aktiváld a Virtual Threadeket a konfigurációban.
4. Kontroller nem ad vissza Entitást, csak DTO-t (MapStruct-tal mapelve).
5. A hibakezelés RFC 7807 (`ProblemDetail`).
6. Tesztelésnél JUnit 5 + AssertJ, integrációs tesztnél nincs H2 (inkább Testcontainers/valós DB).

# Tesztelési Követelmények és Szabályok

## Technológiai Stack

- **Keretrendszer:** JUnit 5
- **Mockolás:** Mockito
- **Assertion:** AssertJ

## Egységtesztek (Unit Tests)

- **Szerviz réteg:** A szervizek üzleti logikáját unit tesztekkel kell lefedni, a külső függőségeket (pl. repository-k) Mockito segítségével mockolva.
- **Izoláció:** A unit tesztek ne indítsák el a Spring kontextust, maradjanak gyorsak és izoláltak.

## Integrációs Tesztek (Integration Tests)

- **Spring Kontextus:** Az integrációs tesztekhez használd a `@SpringBootTest` annotációt.
- **Adatbázis Környezet:** Kerüld az in-memory (pl. H2) adatbázisok használatát, ha a production környezet MSSQL-t használ. Helyette használj valós adatbázis-példányokat (pl. Testcontainers segítségével), hogy a tesztkörnyezet megegyezzen a production-nal.
