**1. MVC projekt letrehozasa**
Asp.net core web app with MVC


**2. Nuget csomagok telepitese (9.0.11 )**
		
		1. Microsoft.EntityFrameworkCore
		2. Microsoft.EntityFrameworkCore.Sqlite
		3. Microsoft.EntityFrameworkCore.Tools 
	
**3. Adatbázis létrehozása konzolon **

	cd "drive":
	cd destination
	sqlite3 database.db

----------------Zh Start-------------------------

**4.Modell osztályok létrehozása**


    namespace vizibusz.Models;
    using System.ComponentModel.DataAnnotations;

    public enum Manufacturers { Airbus, Boeing, Embraer }
    public class Airplane
    {
        public int Id { get; set; }
    
        [Required]
        [StringLength(8)]
        [RegularExpression(@"^[ABE][0-9]{3}-[0-9]{3}$",
            ErrorMessage = "A név formátuma: %###-### (A/B/E + 3 szám + '-' + 3 szám)")]
        public string Name { get; set; }
    
        [Required]
        public Manufacturers Manufacturer { get; set; }
    
        [Range(0, 2)]
        public float MaxSpeed { get; set; }  // Mach
    
        [Required]
        public DateTime TechnicalValidity { get; set; }
    
        public virtual ICollection<Staff>? RefCrew { get; set; } = new List<Staff>();
    
    
    }


---------------------------------------------

    using System.ComponentModel.DataAnnotations;
    using System.ComponentModel.DataAnnotations.Schema;
    
    namespace vizibusz.Models
    {
        public class Staff
        {
            public int Id { get; set; }
    
            [Required]
            public string Name { get; set; }
    
            public bool IsCaptain { get; set; }
    
            [Required]
            [ForeignKey("Airplane")] //18-as sorban lévő Airplane property-hez kapcsolódik a neve meg kell egyezzen
            public int AirplaneId { get; set; }
    
            public virtual Airplane? RefAirplane { get; set; }
        }
    }

*hasznos még:* 
*Érdemes nullable (pl. int?) használata. 
Annotációk: [Key] [Required] [Range(16,40)] [Display(Name = “Name”)] [ForeignKey(“HivatkozottCsapat”)]*

**5. Context osztály**
Context mappa létrehozása
EFContext.cs létrehozása
appsettings.json connection string létrehozása


db linkelése > két lehetőség:
	
	1. EFContext-ben
	2. appsettings.json-ban

majd modositjuk a program.cs-t is

	 

    //builder.Services.AddDbContext<vizibusz.Context.EFContext>();
     //vagy
     builder.Services.AddDbContext<vizibusz.Context.EFContext>(options =>
         options.UseSqlite(builder.Configuration.GetConnectionString("DefaultConnection")));

**6. Migráció**
Add-Migration ...
Update-Database

**7. Controllerek létrehozása**
