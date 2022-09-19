# 1 Mediatori

## 1.0 Introduzione
I mediatori (broker) sono un collegamento tra la logica aziendale e il mondo esterno.
Sono wrapper attorno a librerie, risorse, servizi o API esterne per soddisfare un'interfaccia locale per consentire all'azienda di interagire con esse senza dover essere strettamente accoppiate con risorse particolari o implementazione di librerie esterne.

I broker, in generale, sono pensati per essere usa e getta e sostituibili: sono costruiti con la consapevolezza che la tecnologia si evolve e cambia continuamente. Pertanto, a un certo punto del ciclo di vita di una determinata applicazione, verrà sostituita con una tecnologia recente che consente di svolgere il lavoro più rapidamente.

Ma i broker assicurano anche che la tua attività sia collegabile astraendo qualsiasi specifica dipendenza da risorse esterne da ciò che il tuo software sta cercando di realizzare.

Ad esempio, supponiamo che tu abbia un'API creata per consumare e fornire dati da un server SQL. Ad un certo punto, hai deciso che un'opzione migliore e più economica per la tua API è affidarsi invece a una tecnologia NoSql. Avere un broker per rimuovere la dipendenza da SQL renderà molto più semplice l'integrazione con NoSql con il minor tempo e costo umanamente possibile.

## 1.1 Sulla mappa
In una determinata applicazione, mobile, desktop, web o semplicemente un'API, i broker di solito risiedono alla "coda" di qualsiasi app perché sono l'ultimo punto di contatto tra il nostro codice personalizzato e il mondo esterno.

Indipendentemente dal fatto che il mondo esterno in questo caso sia semplicemente un'archiviazione locale in memoria o un sistema indipendente che risiede dietro un'API, devono risiedere tutti dietro Broker in qualsiasi applicazione.

Nella seguente architettura di basso livello per una determinata API, i broker risiedono tra la nostra logica aziendale e la risorsa esterna:

<br />
    <p align=center>
        <img src="https://user-images.githubusercontent.com/1453985/148864693-d432be6f-13b1-4def-af95-9c63af430802.png" />
    </p>
<br />

## 1.2 Caratteristiche
Ci sono alcune semplici regole che regolano l'implementazione di qualsiasi broker: queste regole sono:

### 1.2.0 Implementa un'interfaccia locale
I broker devono soddisfare un contratto locale. Devono implementare un'interfaccia locale per consentire il disaccoppiamento tra la loro implementazione ei servizi che li consumano.

Ad esempio, dato che abbiamo un contratto locale "IStorageBroker" che richiede un'implementazione per una determinata operazione CRUD per un modello locale "Studente", l'operazione contrattuale sarebbe la seguente:

```acuto
    interfaccia pubblica parziale IStorageBroker
    {
        IQueryable<Studente> SelectAllStudents();
    }
```

Un'implementazione per un broker di archiviazione sarebbe la seguente:

```acuto
    classe parziale pubblica StorageBroker
    {
        public DbSet<Studente> Studenti { get; impostare; }

        public IQueryable<Student> SelectAllStudents()
        {
            using var broker = new StorageBroker(this.configuration);

            broker di ritorno.Studenti;
        }
    }
```
Un'implementazione del contratto locale può essere sostituita in qualsiasi momento, dall'utilizzo di Entity Framework come mostrato nell'esempio precedente all'utilizzo di una tecnologia completamente diversa come Dapper o di un'infrastruttura completamente diversa come un database Oracle o PostgreSQL.

### 1.2.1 Nessun controllo di flusso
I broker non dovrebbero avere alcuna forma di controllo del flusso come if-statements, while-loops o switch case, perché il codice di controllo del flusso è considerato una logica aziendale e si adatta meglio al livello dei servizi in cui dovrebbe risiedere la logica aziendale, non i broker.

Ad esempio, un metodo broker che recupera un elenco di studenti da un database sarebbe simile a questo:

```acuto
    public IQueryable<Student> SelectAllStudents()
    {
        using var broker = new StorageBroker(this.configuration);

        broker di ritorno.Studenti;
    }
```
Una semplice funzione che chiama EntityFramework nativo `DbSet<T>` e restituisce un modello locale come `Student`.