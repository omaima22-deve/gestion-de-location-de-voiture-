// gestion-de-location-de-voiture-

#include<stdio.h>
#include<stdlib.h>
#include<time.h>
#include<string.h>
#include<conio.h>
#include<windows.h>




typedef struct date {
 int jour;
 int mois;
 int annee;
    }date;

typedef struct Voiture
{
int idVoiture;
char marque[15];
char nomVoiture[15];
char couleur[7];
int nbplaces;
int prixJour;
char EnLocation[4];
} voiture;

typedef struct contratLocation
{
float numContrat;
int idVoiture;
int idClient;
date debut;
date fin;
int cout;
} contrat;

typedef struct Client
{
int idClient;
char nom[20];
char prenom[20];
int cin;
char adresse[15];
int telephone;
}client;


const char*contrat_t="%07.f %09d %09d %02d/%02d/%04d %02d/%02d/%04d %09d\n";
const char* client_t="%09d %19s %19s %07d %14s %010d\n" ;
const char* voiture_t="%09d %14s %14s %6s %02d %06d %3s\n";
int contrat_t_LENGTH = 54;

FILE* voitures;
FILE* contrats;
FILE* clients;
FILE* tempcontrats;
FILE* tempvoitures;
FILE* tempclients;

void MenuPrincipale();void menuCLIENT();void menuLOCATION();void mgv();

int trouver_contrat(FILE*contrats,float numcontr){

    float numscan=-96;
    int x=0;
     while(  x=fscanf(contrats,"%f",&numscan)!= EOF ){ 
        fseek(contrats,contrat_t_LENGTH ,SEEK_CUR);

        if(numscan == numcontr){break;}
    };

    if(numscan != numcontr){
            return 0 ;};

    fseek(contrats,-53,SEEK_CUR);
    return 1;

    }

int trouver_voiture(FILE*voitures,int idvoit){

    printf("searching for voiture ...\t");
    voiture* pointeurvoiture=(voiture*)malloc(sizeof(voiture));

    while(!feof(voitures)){
            fscanf(voitures,"%d %s %s %s %d %d %s",&(pointeurvoiture)->idVoiture,&(pointeurvoiture)->marque,&(pointeurvoiture)->nomVoiture,&(pointeurvoiture)->couleur,&(pointeurvoiture)->nbplaces,&(pointeurvoiture)->prixJour,&(pointeurvoiture)->EnLocation);
            
            if((pointeurvoiture)->idVoiture == idvoit) break;
    }

    if((pointeurvoiture)->idVoiture!=idvoit){printf("\tVoiture NOT FOUND !\n\n");free(pointeurvoiture);return 0 ;};

    fseek(voitures,-9,SEEK_CUR);
    free(pointeurvoiture);
    printf("\tVoiture FOUND !\n");
    return 1;
    }

float trouverlastnumcont(FILE*contrats){
    rewind(contrats);
    float lastnumcont=-316;
    contrat* pointeurcontrat=(contrat*)malloc(sizeof(contrat));

 while(!feof(contrats)){
            fscanf(contrats,"%f %d %d %d/%d/%d %d/%d/%d %d\n",&(pointeurcontrat)->numContrat,&(pointeurcontrat)->idVoiture,&(pointeurcontrat)->idClient,&(pointeurcontrat)->debut.jour,&(pointeurcontrat)->debut.mois,&(pointeurcontrat)->debut.annee,&(pointeurcontrat)->fin.jour,&(pointeurcontrat)->fin.mois,&(pointeurcontrat)->fin.annee,&(pointeurcontrat)->cout);
 }
    lastnumcont=(pointeurcontrat)->numContrat;
  return(lastnumcont);
 }

int trouver_client(FILE*clients,int idcli){

    printf("searching for client ...\t");
    client* pointeurclient=(client*)malloc(sizeof(client));

    while(!feof(clients)){
            fscanf(clients,"%d %s %s %d %s %d",&(pointeurclient)->idClient,&(pointeurclient)->nom,&(pointeurclient)->prenom,&(pointeurclient)->cin,&(pointeurclient)->adresse,&(pointeurclient)->telephone);
            //printf("%d\t",(pointeurclient)->idClient);
            if((pointeurclient)->idClient == idcli) break;
    }

    if((pointeurclient)->idClient!=idcli){printf("\tClient NOT FOUND !\n\n");free(pointeurclient);return 0 ;};

    fseek(clients,-83,SEEK_CUR);
    free(pointeurclient);
    printf("\tClient FOUND !\n");
    return 1;
    }

int trouver_clientnom(FILE*clients,char nomcli[20],char prenomcli[20]){

    printf("searching for client ...\t");
    client* pointeurclient=(client*)malloc(sizeof(client));
   // fseek(clients)
    while(!feof(clients)){
            fscanf(clients,"%d %19s %19s %d %s %d\n",&(pointeurclient)->idClient,&(pointeurclient)->nom,&(pointeurclient)->prenom,&(pointeurclient)->cin,&(pointeurclient)->adresse,&(pointeurclient)->telephone);
           
            if((strcmp((pointeurclient)->nom , nomcli)==0) && (strcmp((pointeurclient)->prenom , prenomcli)==0)) break;
    }

    if((strcmp((pointeurclient)->nom , nomcli)!=0) || (strcmp((pointeurclient)->prenom , prenomcli)!=0)){printf("\tClient NOT FOUND !\n\n");free(pointeurclient);return 0 ;};

    fseek(clients,-85,SEEK_CUR);
    free(pointeurclient);
    printf("\tClient FOUND !\n");
    return 1;
    }

int trouver_voiturenom(FILE*voitures,char marque[15],char nomVoiture[15]){
    printf("searching for voiture ...\t");
    voiture* pointeurvoiture=(voiture*)malloc(sizeof(voiture));
    while(!feof(voitures)){
            fscanf(voitures,"%d %s %s %s %d %d %s",&(pointeurvoiture)->idVoiture,&(pointeurvoiture)->marque,&(pointeurvoiture)->nomVoiture,&(pointeurvoiture)->couleur,&(pointeurvoiture)->nbplaces,&(pointeurvoiture)->prixJour,&(pointeurvoiture)->EnLocation);
            if((strcmp((pointeurvoiture)->marque , marque)==0) && (strcmp((pointeurvoiture)->nomVoiture , nomVoiture)==0)) break;
    }

    if((strcmp((pointeurvoiture)->marque , marque)!=0) || (strcmp((pointeurvoiture)->nomVoiture , nomVoiture)!=0)){printf("\tVoiture NOT FOUND !\n\n");free(pointeurvoiture);return 0 ;};

    fseek(voitures,-61,SEEK_CUR);
    free(pointeurvoiture);
    printf("Voiture FOUND !\n");
    return 1;
    }

/*>>>>>>>>>>>>>>>>>>>-------------------------- MENU DE LOCATION-------------------------- <<<<<<<<<<<<<<<<<<<<<<<<<<<<<< */
void visucontr(){
    contrats = fopen("contrats.txt","r");
    getchar();
    float numcontr;
    system("cls");
    printf("\n\n\nSaisir le num de contrat :");
    scanf("%f",&numcontr);

    if(trouver_contrat(contrats, numcontr)==0){
            printf("\nCe contrat n'existe pas dans notre base de donnees.\n");fclose(contrats); return ;
           };

    char* tab[5]={"id Voiture", "id Client" ,"date debut","date fin", "cout"};
    int i =0;
    char* temp;
    for(i=0;i<5;i++){
      printf("%s :",tab[i]);
      fscanf(contrats,"%s",temp);
      printf("%s",temp);
      printf("\t");
    };

    fclose(contrats);
   return; 
    }

void louevoit(){
// EXISTENCE CLIENT
    if((clients=fopen("clients.txt","r")) == NULL){fprintf(stderr,"\n\aCouldn't open/create \"clients.txt\" !");return;};
    char nomcli[20];
    char prenomcli[20];
    system("cls");
    printf("\nSaisir votre nom :");
    scanf("%19s",nomcli);
    printf("\nSaisir votre prenom:");
    scanf("%19s",prenomcli);

    printf("\n%s\t%s\n",nomcli,prenomcli);
    if(trouver_clientnom(clients,nomcli,prenomcli)==0) {printf("Ce nom et prenom ne correspondent ? aucun client !\nVeuillez vous enregistrer dans le menu client !");return;}

    int idcli;
    fscanf(clients,"%d",&idcli);


// EXISTENCE VOITURE
    printf("\n\nQu'elle voiture voulez vous louer ?\n");
    if((voitures=fopen("voitures.txt","r+")) == NULL){fprintf(stderr,"\n\aCouldn't open/create \"voitures.txt\" !");return;};
    char marque[20];
    char nomVoiture[20];
    printf("\nSaisir la marque [CaSe SeNsItIvE !] :");
    scanf("%19s",marque);
    printf("\nSaisir le nom de la voiture [CaSe SeNsItIvE !]:");
    scanf("%19s",nomVoiture);

    if(trouver_voiturenom(voitures,marque,nomVoiture)==0) {printf("Cette marque et nom de Voiture ne correspondent a aucune voiture !\nVeuillez voir les voitures disponibles dans le menu voiture :)");return;}
    int idvoit=0;
    fscanf(voitures,"%d",&idvoit);
    //printf("\nid voit is : %d\n",idvoit);

//DISPONIBILITE VOITURE
    rewind(voitures);
    if(trouver_voiture(voitures, idvoit)==0) {printf("\nLa voiture dans ce contrat n'existe pas dans notre base de donnees.\n");fclose(voitures);fclose(contrats); fclose(tempcontrats);return ;}
    fseek(voitures,5,SEEK_CUR);

    int i=0;
    char enlocation[20];
    fscanf(voitures,"%s",enlocation); //printf("\t%s\t",enlocation);
    if(strcmp(enlocation,"OUI")==0) {printf("\n\n%s %s n'est pas disponible pour le moment .",marque,nomVoiture);return;}

//LOCATION DE LA VOITURE (ajout d'une contrat)
    printf("\n%s %s est disponible.\n",marque,nomVoiture);
    fseek(voitures,-3,SEEK_CUR);
    fprintf(voitures,"OUI");
    fseek(voitures,-10,SEEK_CUR);
    int cout=0;
    fscanf(voitures,"%d",&cout); printf("\nLe cout est %d/jour\n",cout);

    printf("\nEntrez les dates de debut et fin de contrat:\n");
    contrat* newcontrat=NULL;
    newcontrat=(contrat*)malloc(sizeof(contrat));

    //NEW DATA
    if((voitures=fopen("voitures.txt","r+")) == NULL){fprintf(stderr,"\n\aCouldn't open/create \"voitures.txt\" !");return;};
    contrats = fopen("contrats.txt","r");

    (newcontrat->numContrat)=(trouverlastnumcont(contrats)+1);

    (newcontrat->idVoiture)=idvoit;
    (newcontrat->idClient)=idcli;
    //date debut
    printf("\nDate de debut jour (jj)  :\t");scanf("%d",&(newcontrat->debut.jour));
    printf("\nDate de debut mois (mm)  :\t");scanf("%d",&(newcontrat->debut.mois));
    printf("\nDate de debut annee(aaaa):\t");scanf("%d",&(newcontrat->debut.annee));
    //date fin
    printf("\nDate de fin jour (jj) :\t");scanf("%d",&(newcontrat->fin.jour));
    printf("\nDate de fin mois (mm) :\t");scanf("%d",&(newcontrat->fin.mois));
    printf("\nDate de fin annee(aaaa):\t");scanf("%d",&(newcontrat->fin.annee));

    (newcontrat->cout)=cout*((newcontrat->fin.jour)-(newcontrat->debut.jour)+(newcontrat->fin.mois)-(newcontrat->debut.mois)+(newcontrat->fin.annee)-(newcontrat->debut.annee));
    printf("\nLe cout total a payer est %d dhs\n",newcontrat->cout);
    fclose(voitures);
    contrats=fopen("contrats.txt","a+");
    fprintf(stdout,contrat_t,(newcontrat)->numContrat,(newcontrat)->idVoiture,((newcontrat))->idClient,((newcontrat))->debut.jour,((newcontrat))->debut.mois,((newcontrat))->debut.annee,((newcontrat))->fin.jour,((newcontrat))->fin.mois,((newcontrat))->fin.annee,((newcontrat))->cout);
    fprintf(contrats,contrat_t,(newcontrat)->numContrat,(newcontrat)->idVoiture,((newcontrat))->idClient,((newcontrat))->debut.jour,((newcontrat))->debut.mois,((newcontrat))->debut.annee,((newcontrat))->fin.jour,((newcontrat))->fin.mois,((newcontrat))->fin.annee,((newcontrat))->cout);
    printf("/n/nRouvrir le programme pour visualiser les changements !/n/n");
    fclose(contrats);
    menuLOCATION();return;
    }

void retouvoit(){
    contrats = fopen("contrats.txt","r+");
    if(contrats==NULL) {fprintf(stderr,"\n\aCouldn't open/create \"contrats.txt\" !");return;}
    voitures = fopen("voitures.txt","r+");
    if(voitures==NULL) {fprintf(stderr,"\n\aCouldn't open/create \"voitures.txt\" !");return;}
    tempcontrats = fopen("tempcontrats.dat","w+");
    if(tempcontrats==NULL) {fprintf(stderr,"\n\aCouldn't open/create \"tempcontrats.txt\" !");return;}

    float numcontr;
    int idvoit;
    system("cls");
    printf("\nSaisir le num de contrat :");
    scanf("%f",&numcontr);
    if(trouver_contrat(contrats, numcontr)==0) {printf("\nLe contrat que vouz avez donn? n'existe pas dans notre base de donnees.\n");fclose(contrats);fclose(voitures); fclose(tempcontrats);return ;}
    fscanf(contrats,"%d",&idvoit);
    fseek(contrats,-9,SEEK_CUR);
    if(trouver_voiture(voitures, idvoit)==0) {printf("\nLa voiture dans ce contrat n'existe pas dans notre base de donnees.\n");fclose(voitures);fclose(contrats); fclose(tempcontrats);return ;}

    int i=0;
    char enlocation[20];
    for(i=0;i<2;i++) {fscanf(voitures,"%s",enlocation); printf("\t%s\t",enlocation);}
    if(enlocation=="NON"){printf("\nLa voiture dans ce contrat n'est pas en location !\n");fclose(voitures);fclose(contrats); fclose(tempcontrats);return ;}

    fseek(voitures,-3,SEEK_CUR);
    fprintf(voitures,"NON");

    fclose(contrats);
    contrats=fopen("contrats.txt","r+");
    contrat* pointeurcontrat=(contrat*)malloc(sizeof(contrat));

      while(!feof(contrats)){
            fscanf(contrats,"%f %d %d %d/%d/%d %d/%d/%d %d\n",&(pointeurcontrat)->numContrat,&(pointeurcontrat)->idVoiture,&(pointeurcontrat)->idClient,&(pointeurcontrat)->debut.jour,&(pointeurcontrat)->debut.mois,&(pointeurcontrat)->debut.annee,&(pointeurcontrat)->fin.jour,&(pointeurcontrat)->fin.mois,&(pointeurcontrat)->fin.annee,&(pointeurcontrat)->cout);
            if((pointeurcontrat)->numContrat!=numcontr)  fprintf(tempcontrats,contrat_t,(pointeurcontrat)->numContrat,(pointeurcontrat)->idVoiture,((pointeurcontrat))->idClient,((pointeurcontrat))->debut.jour,((pointeurcontrat))->debut.mois,((pointeurcontrat))->debut.annee,((pointeurcontrat))->fin.jour,((pointeurcontrat))->fin.mois,((pointeurcontrat))->fin.annee,((pointeurcontrat))->cout);
         }

        fclose(voitures);
        fclose(contrats);
        free(pointeurcontrat);
        fclose(tempcontrats);
        remove("contrats.txt");
        rename("tempcontrats.dat","contrats.txt");
        printf("\n\aDONE !\n");
        menuLOCATION();return;
}

void modcontr(){
    contrats = fopen("contrats.txt","r+");
    float numcontr;
    system("cls");
    printf("\nSaisir le num de contrat :");
    scanf("%f",&numcontr);

    if(trouver_contrat(contrats, numcontr)==0){
            printf("\nLe contrat qui a pour ID : %d n'existe pas dans notre base de donnees.\n",numcontr);fclose(contrats); return ;
           };

    char* tab[5]={"id Voiture", "id Client" ,"date debut","date fin", "cout"};
    int i =0;
    char temp[20];
    printf("Les anciens donnees :\n");
    for(i=0;i<5;i++){
      printf("%s :",tab[i]);
      fscanf(contrats,"%s",temp);
      printf("%s",temp);
      printf("\t");
    };

   printf("\nEntrez les nouvelles donnees :\n");
    contrat* newcontrat=NULL;
    newcontrat=(contrat*)malloc(sizeof(contrat));

    //NEW DATA
    (newcontrat->numContrat)=numcontr;

    printf("\n%s :\t",tab[0]);scanf("%d",&(newcontrat->idVoiture));
    printf("\n%s :\t",tab[1]);scanf("%d",&(newcontrat->idClient));
    //date debut
    printf("\n%s jour (jj)  :\t",tab[2]);scanf("%d",&(newcontrat->debut.jour));
    printf("\n%s mois (mm)  :\t",tab[2]);scanf("%d",&(newcontrat->debut.mois));
    printf("\n%s annee(aaaa):\t",tab[2]);scanf("%d",&(newcontrat->debut.annee));
    //date fin
    printf("\n%s jour (jj) :\t",tab[3]);scanf("%d",&(newcontrat->fin.jour));
    printf("\n%s mois (mm) :\t",tab[3]);scanf("%d",&(newcontrat->fin.mois));
    printf("\n%s annee(aaaa):\t",tab[3]);scanf("%d",&(newcontrat->fin.annee));

    printf("\n%s :\t",tab[4]);scanf("%d",&(newcontrat->cout));

    //WRITE NEW DATA TO FILE
    rewind(contrats);
    tempcontrats=fopen("tempcontrats.dat","w");
      if(tempcontrats==NULL) {fprintf(stderr,"\n\aCouldn't open/create \"tempcontrats.txt\" !");return;}

    contrat* pointeurcontrat=(contrat*)malloc(sizeof(contrat));

      while(!feof(contrats)){
            fscanf(contrats,"%f %d %d %d/%d/%d %d/%d/%d %d\n",&(pointeurcontrat)->numContrat,&(pointeurcontrat)->idVoiture,&(pointeurcontrat)->idClient,&(pointeurcontrat)->debut.jour,&(pointeurcontrat)->debut.mois,&(pointeurcontrat)->debut.annee,&(pointeurcontrat)->fin.jour,&(pointeurcontrat)->fin.mois,&(pointeurcontrat)->fin.annee,&(pointeurcontrat)->cout);
            if((pointeurcontrat)->numContrat==numcontr) {fprintf(tempcontrats,contrat_t,(newcontrat)->numContrat,(newcontrat)->idVoiture,((newcontrat))->idClient,((newcontrat))->debut.jour,((newcontrat))->debut.mois,((newcontrat))->debut.annee,((newcontrat))->fin.jour,((newcontrat))->fin.mois,((newcontrat))->fin.annee,((newcontrat))->cout);}
            else {fprintf(tempcontrats,contrat_t,(pointeurcontrat)->numContrat,(pointeurcontrat)->idVoiture,((pointeurcontrat))->idClient,((pointeurcontrat))->debut.jour,((pointeurcontrat))->debut.mois,((pointeurcontrat))->debut.annee,((pointeurcontrat))->fin.jour,((pointeurcontrat))->fin.mois,((pointeurcontrat))->fin.annee,((pointeurcontrat))->cout);
         }}

    free(newcontrat);
    free(pointeurcontrat);
    fclose(contrats);
    fclose(tempcontrats);
    remove("contrats.txt");
    rename("tempcontrats.dat","contrats.txt");
    printf("\n\aDONE !\n");
    menuLOCATION();return;
}

void suppcontr(){
    contrats = fopen("contrats.txt","r+");
    if(contrats==NULL) {fprintf(stderr,"\n\aCouldn't open/create \"contrats.txt\" !");return;}
    voitures = fopen("voitures.txt","r+");
    if(voitures==NULL) {fprintf(stderr,"\n\aCouldn't open/create \"voitures.txt\" !");return;}
    tempcontrats = fopen("tempcontrats.dat","w+");
    if(tempcontrats==NULL) {fprintf(stderr,"\n\aCouldn't open/create \"tempcontrats.txt\" !");return;}

    float numcontr;
    int idvoit;
    system("cls");
    printf("\nSaisir le num de contrat :");
    scanf("%f",&numcontr);
    if(trouver_contrat(contrats, numcontr)==0) {printf("\nCe contrat n'existe pas dans notre base de donnees.\n");fclose(contrats);fclose(voitures); fclose(tempcontrats);return ;}
    fscanf(contrats,"%d",&idvoit);
    fseek(contrats,-9,SEEK_CUR);
    if(trouver_voiture(voitures, idvoit)==0) {printf("\nLa voiture dans ce contrat n'existe pas dans notre base de donnees.\n");fclose(voitures);fclose(contrats); fclose(tempcontrats);return ;}

    int i=0;
    char enlocation[20];
    for(i=0;i<2;i++) {fscanf(voitures,"%s",enlocation); printf("\t%s\t",enlocation);}
    if(enlocation=="OUI"){printf("\nLa voiture dans ce contrat n'est pas rendue ! \n");fclose(voitures);fclose(contrats); fclose(tempcontrats);return ;}

    rewind(contrats);
    contrat* pointeurcontrat=(contrat*)malloc(sizeof(contrat));

      while(!feof(contrats)){
            fscanf(contrats,"%f %d %d %d/%d/%d %d/%d/%d %d\n",&(pointeurcontrat)->numContrat,&(pointeurcontrat)->idVoiture,&(pointeurcontrat)->idClient,&(pointeurcontrat)->debut.jour,&(pointeurcontrat)->debut.mois,&(pointeurcontrat)->debut.annee,&(pointeurcontrat)->fin.jour,&(pointeurcontrat)->fin.mois,&(pointeurcontrat)->fin.annee,&(pointeurcontrat)->cout);
            if((pointeurcontrat)->numContrat!=numcontr)  fprintf(tempcontrats,contrat_t,(pointeurcontrat)->numContrat,(pointeurcontrat)->idVoiture,((pointeurcontrat))->idClient,((pointeurcontrat))->debut.jour,((pointeurcontrat))->debut.mois,((pointeurcontrat))->debut.annee,((pointeurcontrat))->fin.jour,((pointeurcontrat))->fin.mois,((pointeurcontrat))->fin.annee,((pointeurcontrat))->cout);
         }

        fclose(voitures);
        fclose(contrats);
        free(pointeurcontrat);
        fclose(tempcontrats);
        remove("contrats.txt");
        rename("tempcontrats.dat","contrats.txt");
        printf("\n\aDONE !\n");
    menuLOCATION();return;

    }

void (*optionmenuLOCATION[6])()={visucontr,louevoit,retouvoit,modcontr,suppcontr,MenuPrincipale};

void menuLOCATION(){
int choixmenuLOCATION = 6;
do{	system("cls");

	printf("\n                               \xda\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xbf");
	printf("\n                               \xb3   MENU LOCATION \xb3");
	printf("\n                               \xc0\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xd9");

	printf("\n\n");

	printf("\n               \xc9\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xbb");
	printf("\n               \xba                                              \xba");
	printf("\n               \xba    Visualiser contrat....................1   \xba");
	printf("\n               \xba    Louer voiture.........................2   \xba");
	printf("\n               \xba    Retourner voiture.....................3   \xba");
	printf("\n               \xba    Modifier contrat......................4   \xba");
	printf("\n               \xba    Supprimer contrat.....................5   \xba");
	printf("\n               \xba    Retour................................6   \xba");
	printf("\n               \xba                                              \xba");
	printf("\n               \xc8\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xbc");
	printf("\n\n                                Votre choix  :  ");


scanf("%d",&choixmenuLOCATION);
}while(choixmenuLOCATION<1 || choixmenuLOCATION>6);

    (*optionmenuLOCATION[choixmenuLOCATION-1])();printf("\n\nDONE ! \a");
    printf("\033[1;34m");
    printf("\n\n\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd");
    printf("\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd");
    printf("\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd");
    printf("\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\n");
    int i=20;
    printf("Going back to the main menu in :");
    for(i=20;i>0;i--)
    {printf("\t%d\t...",i); Sleep(1000);}

    MenuPrincipale();
}

////////////////////////////////
/*------------------------------- GESTION DE VOITURE  -----------------------------*/

void affvoit(){

    if((voitures=fopen("voitures.txt","r")) == NULL){fprintf(stderr,"\n\aCouldn't open/create \"voitures.txt\" !");return;};
    char caractereVoiture;

    char* tab[7]={"IdVoiture", "Marque" ,"nom Voiture","Color","NP", "PrixJr", "EnLocation"};


    int i =0;
    char* temp;
    system("cls");
    for(i=0;i<7;i++){
        switch(i){
            case 0: printf("\n\n\n%9s ",tab[i]);break;
            case 1: printf("%14s ",tab[i]);break;
            case 2: printf("%14s ",tab[i]);break;
            case 3: printf("%6s ",tab[i]);break;
            case 4: printf("%s ",tab[i]);break;
            case 5: printf("%s ",tab[i]);break;
            case 6: printf("%s \n",tab[i]);break;
    }};

do
{
caractereVoiture = fgetc(voitures);
printf("%c", caractereVoiture);
} while (caractereVoiture != EOF);

    fclose(voitures);


    return;
}

void ajouvoit(){
    system("cls");
    printf("\n\n\n\nLes voitures deja enregistrees : \n");
    affvoit();
    voitures = fopen("voitures.txt","a+");

    printf("\nEntrez les donnees de la nouvelle voiture:\n");
    voiture* newvoiture=NULL;
    newvoiture=(voiture*)malloc(sizeof(voiture));

    //NEW DATA
    char* tab[7]={"IdVoiture", "Marque" ,"nom Voiture","Color","NP", "PrixJr", "EnLocation"};
    getchar();
    printf("\n%s :\t",tab[0]);scanf("%14dc",&(newvoiture->idVoiture));
    printf("\n%s :\t",tab[1]);scanf("%14s",&(newvoiture->marque));
    printf("\n%s :\t",tab[2]);scanf("%14s",&(newvoiture->nomVoiture));
    printf("\n%s :\t",tab[3]);scanf("%6s",&(newvoiture->couleur));
    printf("\n%s :\t",tab[4]);scanf("%2d",&(newvoiture->nbplaces));
    printf("\n%s :\t",tab[5]);scanf("%6d",&(newvoiture->prixJour));
    printf("\n%s [OUI/NON]:\t",tab[6]);scanf("%3s",&(newvoiture->EnLocation));

    fprintf(voitures,voiture_t,(newvoiture)->idVoiture,(newvoiture)->marque,(newvoiture)->nomVoiture,(newvoiture)->couleur,(newvoiture)->nbplaces,(newvoiture)->prixJour,(newvoiture)->EnLocation);
    printf("\n\n");
    int i=0;
        for(i=0;i<7;i++){
        switch(i){
            case 0: printf("\n\n\n%9s ",tab[i]);break;
            case 1: printf("%14s ",tab[i]);break;
            case 2: printf("%14s ",tab[i]);break;
            case 3: printf("%6s ",tab[i]);break;
            case 4: printf("%s ",tab[i]);break;
            case 5: printf("%s ",tab[i]);break;
            case 6: printf("%s \n",tab[i]);break;
    }};
    fprintf(stdout,voiture_t,(newvoiture)->idVoiture,(newvoiture)->marque,(newvoiture)->nomVoiture,(newvoiture)->couleur,(newvoiture)->nbplaces,(newvoiture)->prixJour,(newvoiture)->EnLocation);

    return;}

void modvoit(){
    voitures = fopen("voitures.txt","r+");
    int idvoit;
    printf("\n\n\nSaisir le ID de la voiture :");
    scanf("%d",&idvoit);
    if(trouver_voiture(voitures, idvoit)==0) {printf("\nCette voiture n'existe pas dans notre base de donnees.\n");fclose(voitures);fclose(contrats); fclose(tempcontrats);return ;}
    fseek(voitures,-52,SEEK_CUR);
    char* tab[7]={"IdVoiture", "Marque" ,"nom Voiture","Color","NP", "PrixJr", "EnLocation"};
    int i =0;
    char temp[20];

    printf("\nLes anciens donnees :\n");
    for(i=0;i<7;i++){
      printf("%s :",tab[i]);
      fscanf(voitures,"%s",temp);
      printf("%s",temp);
      printf("\n");
    };

   printf("\nEntrez les nouvelles donnees :\n");
    voiture* newvoiture=NULL;
    newvoiture=(voiture*)malloc(sizeof(voiture));

    //NEW DATA
    (newvoiture->idVoiture)=idvoit;

    printf("\n%s :\t",tab[1]);scanf("%14s",&(newvoiture->marque));
    printf("\n%s :\t",tab[2]);scanf("%14s",&(newvoiture->nomVoiture));
    printf("\n%s :\t",tab[3]);scanf("%6s",&(newvoiture->couleur));
    printf("\n%s :\t",tab[4]);scanf("%2d",&(newvoiture->nbplaces));
    printf("\n%s :\t",tab[5]);scanf("%6d",&(newvoiture->prixJour));
    printf("\n%s [OUI/NON]:\t",tab[6]);scanf("%3s",&(newvoiture->EnLocation));


    //WRITE NEW DATA TO FILE
    rewind(voitures);
    tempvoitures=fopen("tempvoitures.dat","w");
      if(tempvoitures==NULL) {fprintf(stderr,"\n\aCouldn't open/create \"tempvoitures.txt\" !");return;}

    voiture* pointeurvoiture=(voiture*)malloc(sizeof(voiture));


      while(!feof(voitures)){
            fscanf(voitures,"%d %s %s %s %d %d %s",&(pointeurvoiture)->idVoiture,&(pointeurvoiture)->marque,&(pointeurvoiture)->nomVoiture,&(pointeurvoiture)->couleur,&(pointeurvoiture)->nbplaces,&(pointeurvoiture)->prixJour,&(pointeurvoiture)->EnLocation);
            if((pointeurvoiture)->idVoiture==idvoit) {fprintf(tempvoitures,voiture_t,(newvoiture)->idVoiture,(newvoiture)->marque,(newvoiture)->nomVoiture,(newvoiture)->couleur,(newvoiture)->nbplaces,(newvoiture)->prixJour,(newvoiture)->EnLocation);}
            else {fprintf(tempvoitures,voiture_t,(pointeurvoiture)->idVoiture,(pointeurvoiture)->marque,(pointeurvoiture)->nomVoiture,(pointeurvoiture)->couleur,(pointeurvoiture)->nbplaces,(pointeurvoiture)->prixJour,(pointeurvoiture)->EnLocation);
         }}

    free(newvoiture);
    free(pointeurvoiture);
    fclose(voitures);
    fclose(tempvoitures);
    remove("voitures.txt");
    rename("tempvoitures.dat","voitures.txt");
    printf("\n\aDONE !\n");
    return;}

void suppvoit(){
    int idvoit;
    voitures = fopen("voitures.txt","r+");

    printf("\nSaisir le ID de la voiyure a supprimer :\t");
    scanf("%d",&idvoit);
    if(trouver_voiture(voitures, idvoit)==0) {printf("\nCette voiture n'existe pas dans notre base de donnees.\n");fclose(voitures);fclose(voitures); fclose(tempvoitures);return ;}

    fseek(voitures,-52,SEEK_CUR);
    char* tab[7]={"IdVoiture", "Marque" ,"nom Voiture","Color","NP", "PrixJr", "EnLocation"};
    int i =0;
    char temp[20];

    printf("\nLes donnees de la voiture supprimee:\n");
    for(i=0;i<7;i++){
      printf("%s :",tab[i]);
      fscanf(voitures,"%s",temp);
      printf("%s",temp);
      printf("\n");
    };

    tempvoitures = fopen("tempvoitures.dat","w+");


    rewind(voitures);
    voiture* pointeurvoiture=(voiture*)malloc(sizeof(voiture));

      while(!feof(voitures)){
            fscanf(voitures,"%d %s %s %s %d %d %s",&(pointeurvoiture)->idVoiture,&(pointeurvoiture)->marque,&(pointeurvoiture)->nomVoiture,&(pointeurvoiture)->couleur,&(pointeurvoiture)->nbplaces,&(pointeurvoiture)->prixJour,&(pointeurvoiture)->EnLocation);
            if((pointeurvoiture)->idVoiture!=idvoit)  fprintf(tempvoitures,voiture_t,(pointeurvoiture)->idVoiture,(pointeurvoiture)->marque,(pointeurvoiture)->nomVoiture,(pointeurvoiture)->couleur,(pointeurvoiture)->nbplaces,(pointeurvoiture)->prixJour,(pointeurvoiture)->EnLocation);
         }

        fclose(voitures);
        fclose(tempvoitures);
        free(pointeurvoiture);
        fclose(tempvoitures);
        remove("voitures.txt");
        rename("tempvoitures.dat","voitures.txt");
        printf("\n\aDONE !\n");
        mgv();return;
        }

void (*optionmgv[5])()={affvoit,ajouvoit,modvoit,suppvoit,MenuPrincipale};

void mgv(){
int choixmgv = 5;
do{system("cls");
    printf("\n                           \xda\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xbf");
	printf("\n                           \xb3 Gestion des voiture\xb3");
	printf("\n                           \xc0\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xd9");

	printf("\n\n");

	printf("\n               \xc9\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xbb");
	printf("\n               \xba                                              \xba");
	printf("\n               \xba    Visualiser voiture....................1   \xba");
	printf("\n               \xba    Ajouter voiture.......................2   \xba");
	printf("\n               \xba    Modifier voiture......................3   \xba");
	printf("\n               \xba    Supprimer voiture.....................4   \xba");
	printf("\n               \xba    Retour................................5   \xba");
	printf("\n               \xba                                              \xba");
	printf("\n               \xc8\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xbc");
	printf("\n\n                                Votre choix  :  ");

scanf("%d",&choixmgv);
}while(choixmgv<1 || choixmgv>5);
   (*optionmgv[choixmgv-1])();printf("\n\nDONE ! \a");
    printf("\033[1;34m");
    printf("\n\n\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd");
    printf("\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd");
    printf("\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd");
    printf("\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\n");
    int i=20;
    printf("Going back to the main menu in :");
    for(i=20;i>0;i--)
    {printf("\t%d\t...",i); Sleep(1000);}

    MenuPrincipale();
   return;
    }

////////////////////////////////
/* <<<<<<<<<<<<<<<<<-----------------------------GESTION DES CLIENT ----------------->>>>>>>>>>>>>>>>>>>>. */

void affcli(){

    if((clients=fopen("clients.txt","r")) == NULL){fprintf(stderr,"\n\aCouldn't open \"clients.txt\" !");menuCLIENT();return;};
    char caractereClient;

    char* tab[6]={"id Client", "Nom" ,"Prenom","CIN","Adresse", "Telephone"};
    int i =0;
    char* temp;
    for(i=0;i<6;i++){
        switch(i){
            case 0: printf("%7s ",tab[i]);break;
            case 1: printf("%19s ",tab[i]);break;
            case 2: printf("%19s ",tab[i]);break;
            case 3: printf("%7s ",tab[i]);break;
            case 4: printf("%14s ",tab[i]);break;
            case 5: printf("%10s\n",tab[i]);break;
    }};

do
{
caractereClient = fgetc(clients);
printf("%c", caractereClient);
} while (caractereClient != EOF);

    fclose(clients);

    //menuCLIENT();
    return;
}

void ajoucli(){
    clients = fopen("clients.txt","a+");
    if(clients==NULL) {fprintf(stderr,"\n\aCouldn't open/create \"clients.txt\" !");menuCLIENT();return;}

    printf("\nEntrez les donnees du nouveau client:\n");
    client* newclient=NULL;
    newclient=(client*)malloc(sizeof(client));

    //NEW DATA
    char* tab[6]={"idClient", "nom" ,"prenom","cin","adresse", "telephone"};
    getchar();
    printf("\n%s :\t",tab[0]);scanf("%7d",&(newclient->idClient));
    printf("\n%s :\t",tab[1]);scanf("%19s",&(newclient->nom));
    printf("\n%s :\t",tab[2]);scanf("%19s",&(newclient->prenom));
    printf("\n%s [ 8 chiffres MAX (pas de caracteres !)]:\t",tab[3]);scanf("%8d",&(newclient->cin));
    printf("\n%s [14 caracteres MAX (pas d'espace !) virgule autorisee:\t",tab[4]);scanf("%14s",&(newclient->adresse));
    printf("\n%s :\t",tab[5]);scanf("%10d",&(newclient->telephone));

    fprintf(clients,client_t,(newclient)->idClient,(newclient)->nom,(newclient)->prenom,(newclient)->cin,(newclient)->adresse,(newclient)->telephone);
    printf("\nLe nouveau client :\n");
    fprintf(stdout,client_t,(newclient)->idClient,(newclient)->nom,(newclient)->prenom,(newclient)->cin,(newclient)->adresse,(newclient)->telephone);

    free(newclient);
    fclose(clients);
    menuCLIENT();return;}

void modcli(){
    int idcli;
    clients = fopen("clients.txt","r+");
    printf("\nSaisir le ID du client a modifier :\t");
    scanf("%d",&idcli);
    if(trouver_client(clients, idcli)==0) {printf("\nCe client n'existe pas dans notre base de donnees.\n");fclose(clients);fclose(clients); menuCLIENT();return ;}


char* tab[6]={"idClient", "nom" ,"prenom","cin","adresse", "telephone"};
    int i =0;
    char temp[20];
    printf("\nLes anciens donnees :\n");
    for(i=0;i<6;i++){
      printf("%s :",tab[i]);
      fscanf(clients,"%s",temp);
      printf("%s",temp);
      printf("\n");
    };
    //NEW DATA
    printf("\nEntrez les nouvelles donnees :\n");
    client* newclient=NULL;
    newclient=(client*)malloc(sizeof(client));
    (newclient->idClient)=idcli;
    getchar();
    printf("\n%s :\t",tab[0]);scanf("%7d",&(newclient->idClient));
    printf("\n%s :\t",tab[1]);scanf("%19s",&(newclient->nom));
    printf("\n%s :\t",tab[2]);scanf("%19s",&(newclient->prenom));
    printf("\n%s [ 9 chiffres MAX (pas de caracteres !)]:\t",tab[3]);scanf("%9d",&(newclient->cin));
    printf("\n%s [14 caracteres MAX (pas d'espace !) virgule autorisee:\t",tab[4]);scanf("%14s",&(newclient->adresse));
    printf("\n%s :\t",tab[5]);scanf("%10d",&(newclient->telephone));

const char* client_t="%09d %19s %19s %07d %14s %010d\n" ;

    //WRITE NEW DATA TO FILE
    rewind(clients);
    tempclients=fopen("tempclients.dat","w");
      if(tempclients==NULL) {fprintf(stderr,"\n\aCouldn't open/create \"tempclients.txt\" !");menuCLIENT();return;}

    client* pointeurclient=(client*)malloc(sizeof(client));


      while(!feof(clients)){
            fscanf(clients,"%d %s %s %d %s %d",&(pointeurclient)->idClient,&(pointeurclient)->nom,&(pointeurclient)->prenom,&(pointeurclient)->cin,&(pointeurclient)->adresse,&(pointeurclient)->telephone);
            if((pointeurclient)->idClient==idcli) {fprintf(tempclients,client_t,(newclient)->idClient,(newclient)->nom,(newclient)->prenom,(newclient)->cin,(newclient)->adresse,(newclient)->telephone);}
            else {fprintf(tempclients,client_t,(pointeurclient)->idClient,(pointeurclient)->nom,(pointeurclient)->prenom,(pointeurclient)->cin,(pointeurclient)->adresse,(pointeurclient)->telephone);
         }}

        fclose(clients);
        fclose(tempclients);
        free(pointeurclient);
        fclose(tempclients);
        remove("clients.txt");
        rename("tempclients.dat","clients.txt");
        printf("\n\aDONE !\n");
        menuCLIENT();return;}

void suppcli(){
    int idcli;
    clients = fopen("clients.txt","r+");

    printf("\nSaisir le ID du client a supprimer :\t");
    scanf("%d",&idcli);
    if(trouver_client(clients, idcli)==0) {printf("\nCe client n'existe pas dans notre base de donnees.\n");fclose(clients);fclose(clients); return ;}

    tempclients = fopen("tempclients.dat","w+");

    rewind(clients);
    client* pointeurclient=(client*)malloc(sizeof(client));

      while(!feof(clients)){
            fscanf(clients,"%d %s %s %d %s %d",&(pointeurclient)->idClient,&(pointeurclient)->nom,&(pointeurclient)->prenom,&(pointeurclient)->cin,&(pointeurclient)->adresse,&(pointeurclient)->telephone);
                        if((pointeurclient)->idClient!=idcli)  fprintf(tempclients,client_t,(pointeurclient)->idClient,(pointeurclient)->nom,(pointeurclient)->prenom,(pointeurclient)->cin,(pointeurclient)->adresse,(pointeurclient)->telephone);
         }

        fclose(clients);
        fclose(tempclients);
        free(pointeurclient);
        fclose(tempclients);
        remove("clients.txt");
        rename("tempclients.dat","clients.txt");
        printf("\n\aDONE !\n");
        menuCLIENT();return;}

void (*optionmenuCLIENT[5])()={affcli,ajoucli,modcli,suppcli,MenuPrincipale};

void menuCLIENT(){
int choixmenuCLIENT = 5;
do{system("cls");
    printf("\n                               \xda\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xbf");
	printf("\n                               \xb3 Gestion client  \xb3");
	printf("\n                               \xc0\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xd9");

	printf("\n\n");

	printf("\n               \xc9\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xbb");
	printf("\n               \xba                                              \xba");
	printf("\n               \xba    Visualiser client.....................1   \xba");
	printf("\n               \xba    Ajouter client........................2   \xba");
	printf("\n               \xba    Modifier client.......................3   \xba");
	printf("\n               \xba    Supprimer client......................4   \xba");
	printf("\n               \xba    Retour................................5   \xba");
	printf("\n               \xba                                              \xba");
	printf("\n               \xba                                              \xba");
	printf("\n               \xc8\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xbc");
	printf("\n\n                                Votre choix  :  ");
scanf("%d",&choixmenuCLIENT);
}while(choixmenuCLIENT<1 || choixmenuCLIENT>5);

    (*optionmenuCLIENT[choixmenuCLIENT-1])();printf("\n\nDONE ! \a");
    printf("\033[1;34m");
    printf("\n\n\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd");
    printf("\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd");
    printf("\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd");
    printf("\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\n");
    int i=20;
    printf("Going back to the main menu in :");
    for(i=20;i>0;i--)
    {printf("\t%d\t...",i); Sleep(1000);}

    MenuPrincipale();
    }

void quit(){
    printf("\n\n\n\n\n\a By: 0mar.rar ");
    exit(4);
}

void (*option[4])()={menuLOCATION,mgv,menuCLIENT,quit};

void MenuPrincipale(void){
int choix=4;

do{
	system("cls");

	printf("\n                               \xda\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xbf");
	printf("\n                               \xb3 Menu Principale \xb3");
	printf("\n                               \xc0\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xc4\xd9");

	printf("\n\n");

	printf("\n               \xc9\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xbb");
	printf("\n               \xba                                              \xba");
	printf("\n               \xba    Location..............................1   \xba");
	printf("\n               \xba    Gestion des voitures..................2   \xba");
	printf("\n               \xba    Gestion des clients...................3   \xba");
	printf("\n               \xba    Quitter...............................4   \xba");
	printf("\n               \xba                                              \xba");
	printf("\n               \xc8\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xcd\xbc");
	printf("\n\n                                Votre choix  :  ");

scanf("%d",&choix);
}
while(choix<1 || choix>4);

(*option[choix-1])();
}

int main(int argc,char* argv[]){

MenuPrincipale();
    return 0;
}
