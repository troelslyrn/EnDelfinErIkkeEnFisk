# Jeg har valgt at skrive om Sqlite som jeg mener er en god og brugbar løsning når det kommer til at skulle gemme data i din app lokalt, hvilket jeg mener er en god måde at gøre det på hvis det man ikke skal dele data med andre, ydermere mener jeg at det er bedre end at have dataen liggende på en ekstern database, da dataen på en Sqlite database altid er tilgængelig selvom man ikke skulle have adgang til internettet, dog ved jeg godt at dette ikke er et problem for særlig mange mennesker, og hvis hvis man ikke har tænkt sig, at ens app konstant skal sende og modtage data fra en ekstern database, så er dataforbrug heller ikke et problem. 
Dog vil jeg stadig argumentere for at Sqlite har sin plads især hvis man som app udbyder ikke vil betale for at få opbevaret data og ikke vil fokusere særlig meget på sikkerhed, samtidig vil jeg mene at det ikke at have en ekstern database mindsker risikoen for hackerangreb. Jeg ved godt, at det at snakke om hackerangreb ved noget så småt som apps og især apps idet omfang som vi brugere det kan forekomme latterligt, dog er det stadig en ting mindre at bekymre sig om. 

Jeg personligt har tænkt mig at benytte mig af Sqlite i min zombie app til at opbevare data, og til at giver bruger af appen en nem og pålidelig database som jeg har tillid til, ydermere så har jeg arbejdet med sql gennem min uddannelse, hvilket giver mig en vis fortrolighed med sqlite da det minder en del om sql. 
Ydermere har jeg ikke behov for at brugeren af appen skal kunne dele data med andre brugere, og jeg har ikke i sinde at bruge penge på at benytte mig af en ekstern database. 

I min app benytter jeg mig af sqlite på følgende måde.
brugeren vil kunne se ca 3-4 felter hvor på at brugeren kan skrive ting som brugeren vil mere kunne bruges. for eksempel ( kost, kniv, gaffatape).
brugeren vil ved siden af felterne, kunne se en knap der hedder konstruere
når brugeren har sat søgeord ind i felterne, og trykket på knappen konstruere, vil brugen, i et felt under knappen, modtage et svar på hvad de forskellige elementer tilsammen ville danne, i dette tilfælde vil en kost, kniv og gaffatape blive til et spyd, brugerne vil derfor kunne se ordet spyd i resultats feltet som er under knappen konstruere. 
brugeren har så mulighed for at gennem dette “våben”, så brugeren sener kan gå tilbage og kigge på hvilke ting som brugeren havde mulighed for at bygge. Dette gør brugeren ved at trykke på gem, som er ved siden af resultats feltet. 
At sætte det op på denne måde vil fungere på samme måde som hvis man laver en en database med brugere, hvorpå at man kan søge på brugere ved hjælp af brugernavn osv. Dog i min database vil det ikke være brugere der bliver gemt, men en lang række redskaber, somme alle har forskellige elementer/egenskaber som brugeren kan søge på. Dog ved min database vil jeg ikke give muligheden at brugeren kan oprette nye elementer. Brugeren kan kun hente elementer fra databasen, og gennem allerede eksisterende elementer på et “bruger table” så brugeren hurtigt kan finde de de ting som brugeren har mulighed for at lave.   

Jeg har overvejet også at lave et table med en masse string elementer, som skal fungere som små “bøger”/hints til hvordan man overlever. Det skal fungere som følgende:

brugeren vil se x antal knapper (antallet er endnu ikke bestemt)
på knapperne vil brugeren kunne se nogle spørgsmål, som skal fungere som ledende spørgsmål til hvad knappen vil føre brugeren videre til. for eksempel (er du blevet bidt ?, Er du sulten ? , Mangler du et sted at sove i nat ?)
hvis brugeren trykker på en knap vil bruge kunne se at text feltet nedenunder alle knapperne, vil blive fyldt med med et hjælpsomt tekstelement.  
brugeren kan scrolle på teksten, skulle teksten være større end tekstfeltet. 

Det vil fungere på den måde at hver knap er lig med til en metode, der henter et tekstelement fra databasen, dette kunne også havde været løst uden at bruge en database, dog finder jeg det sjover at bruge en database til dette, men for at vise at det kan gøre på flere måder vil jeg muligvis gøre dette, for eksempel kunne man også bare lave en java classe i appen der holder en masse strings og så bare kalde de forskellige strings derfra og ind i den class som søgefunktionen ligge i. 


På de følgende 2 sider vil du kunne læse om hvordan Sqlite fungere, dog er det følgende jeg skriver viden fra timen og artikler jeg har fundet på internettet, dog selvfølgelig ikke direkte skrevet efter. 
Dog hvis du kun er interesseret i hvordan jeg har og har i sinde at benytte mig af sqlite, så er det hvad der står på de 2 første sider, derfor er resten af hvad du vil læse her ikke direkte fra mit projekt.  

Følgende viden er henter fra følgende sidder: https://www.androidauthority.com/creating-sqlite-databases-in-your-app-719366/
https://developer.android.com/training/data-storage/sqlite.html

For at benytte sig af SQLITE der det en god ide at sætte en “companion class” op. I dette tilfælde vil vi kalde klassen for FeedReaderContract. I denne klasse vil man nemmer kunne administrere sin database. 
I dette eksempel er “companion class”’en sat op på følgende måde :
public final class FeedReaderContract {
    // To prevent someone from accidentally instantiating the contract class,
    // make the constructor private.
    private FeedReaderContract() {}

    /* Inner class that defines the table contents */
    public static class FeedEntry implements BaseColumns {
        public static final String TABLE_NAME = "entry";
        public static final String COLUMN_NAME_TITLE = "title";
        public static final String COLUMN_NAME_SUBTITLE = "subtitle";
    }
}

Derefter er det en god ide at lave en SQL helper, det kan man enten gøre igennem en metode eller en class, jeg vil mene at en class er en mere overskuelig måde at gøre det på, så jeg ville gøre det som følgende:

public class SampleDBSQLiteHelper extends SQLiteOpenHelper {

    private static final int DATABASE_VERSION = 1;
    public static final String DATABASE_NAME = "sample_database";

    public SampleDBSQLiteHelper(Context context) {
        super(context, DATABASE_NAME, null, DATABASE_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase sqLiteDatabase) {
        sqLiteDatabase.execSQL(SampleDBContract.Employer.CREATE_TABLE);
    }

    @Override
    public void onUpgrade(SQLiteDatabase sqLiteDatabase, int i, int i1) {
        sqLiteDatabase.execSQL("DROP TABLE IF EXISTS " + SampleDBContract.Employer.TABLE_NAME);
        onCreate(sqLiteDatabase);
    }
}
 nu er det på tide at putte noget indhold ind i vores database, jeg forslår at gøre det på følgende måde : 
   private void saveToDB() {
        SQLiteDatabase database = new SampleDBSQLiteHelper(this).getWritableDatabase();

        ContentValues values = new ContentValues();
        values.put(SampleDBContract.Employer.COLUMN_NAME, binding.nameEditText.getText().toString());
        values.put(SampleDBContract.Employer.COLUMN_DESCRIPTION, binding.descEditText.getText().toString());

        try {
            Calendar calendar = Calendar.getInstance();
            calendar.setTime((new SimpleDateFormat("dd/MM/yyyy")).parse(
                    binding.foundedEditText.getText().toString()));
            long date = calendar.getTimeInMillis();
            values.put(SampleDBContract.Employer.COLUMN_FOUNDED_DATE, date);
        }
        catch (Exception e) {
            Log.e(TAG, "Error", e);
            Toast.makeText(this, "Date is in the wrong format", Toast.LENGTH_LONG).show();
            return;
        }
        long newRowId = database.insert(SampleDBContract.Employer.TABLE_NAME, null, values);

        Toast.makeText(this, "The new Row Id is " + newRowId, Toast.LENGTH_LONG).show();
    }

nu da der er data i vores database, så kan vi vælge at lave metoder til at hente data ud af vores database, jeg foreslår at gøre det på følgende måde:    private void readFromDB() {
        String name = binding.nameEditText.getText().toString();
        String desc = binding.descEditText.getText().toString();
        long date = 0;

        try {
            Calendar calendar = Calendar.getInstance();
            calendar.setTime((new SimpleDateFormat("dd/MM/yyyy")).parse(
                    binding.foundedEditText.getText().toString()));
            date = calendar.getTimeInMillis();
        }
        catch (Exception e) {}

        SQLiteDatabase database = new SampleDBSQLiteHelper(this).getReadableDatabase();

        String[] projection = {
                SampleDBContract.Employer._ID,
                SampleDBContract.Employer.COLUMN_NAME,
                SampleDBContract.Employer.COLUMN_DESCRIPTION,
                SampleDBContract.Employer.COLUMN_FOUNDED_DATE
        };

        String selection =
                SampleDBContract.Employer.COLUMN_NAME + " like ? and " +
                        SampleDBContract.Employer.COLUMN_FOUNDED_DATE + " > ? and " +
                        SampleDBContract.Employer.COLUMN_DESCRIPTION + " like ?";

        String[] selectionArgs = {"%" + name + "%", date + "", "%" + desc + "%"};

        Cursor cursor = database.query(
                SampleDBContract.Employer.TABLE_NAME,     // The table to query
                projection,                               // The columns to return
                selection,                                // The columns for the WHERE clause
                selectionArgs,                            // The values for the WHERE clause
                null,                                     // don't group the rows
                null,                                     // don't filter by row groups
                null                                      // don't sort
        );

        Log.d(TAG, "The total cursor count is " + cursor.getCount());
        binding.recycleView.setAdapter(new SampleRecyclerViewCursorAdapter(this, cursor));
    }

nu at du kan se at det faktisk er ganske nemt, så er det bare at gå i krig med at lave din egen sqlite database :) held og lykke. 
