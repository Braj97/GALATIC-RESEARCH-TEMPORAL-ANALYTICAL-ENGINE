# GALATIC-RESEARCH-TEMPORAL-ANALYTICAL-ENGINE
T-SQL
# CREATE DATABASES
CREATE DATABASE GalacticTemporalEngine;
GO

USE GalacticTemporalEngine;
GO
# CORE TABLES
-- Planets
CREATE TABLE Planets (
    PlanetID INT PRIMARY KEY IDENTITY,
    PlanetName NVARCHAR(100) UNIQUE NOT NULL,
    GravityFactor DECIMAL(5,2),
    RadiationLevel INT,
    AtmosphereType NVARCHAR(100),
    CreatedAt DATETIME DEFAULT GETDATE()
);

-- Scientists
CREATE TABLE Scientists (
    ScientistID INT PRIMARY KEY IDENTITY,
    FullName NVARCHAR(150),
    IQLevel INT,
    Specialization NVARCHAR(100),
    EnergySignature DECIMAL(10,2),
    AssignedPlanetID INT,
    FOREIGN KEY (AssignedPlanetID) REFERENCES Planets(PlanetID)
);

-- Missions
CREATE TABLE Missions (
    MissionID INT PRIMARY KEY IDENTITY,
    MissionName NVARCHAR(200),
    ScientistID INT,
    StartDate DATETIME,
    EndDate DATETIME,
    EnergyConsumed DECIMAL(18,2),
    Status NVARCHAR(50),
    FOREIGN KEY (ScientistID) REFERENCES Scientists(ScientistID)
);

-- Energy Logs
CREATE TABLE EnergyLogs (
    LogID INT PRIMARY KEY IDENTITY,
    MissionID INT,
    LogTime DATETIME DEFAULT GETDATE(),
    EnergyOutput DECIMAL(18,2),
    Temperature FLOAT,
    FOREIGN KEY (MissionID) REFERENCES Missions(MissionID)
);
# INSERT SAMPLE DATA
INSERT INTO Planets (PlanetName, GravityFactor, RadiationLevel, AtmosphereType)
VALUES 
('Xenon Prime', 1.8, 450, 'Methane Plasma'),
('Zypherion', 0.6, 120, 'Hydrogen Mist'),
('Orion-X', 2.4, 800, 'Toxic Vapor');

INSERT INTO Scientists (FullName, IQLevel, Specialization, EnergySignature, AssignedPlanetID)
VALUES
('Dr. Aryan Rao', 198, 'Quantum Physics', 5400.50, 1),
('Dr. Meera Sen', 185, 'Astro Biology', 4300.30, 2),
('Dr. Ishaan Verma', 210, 'Dark Energy Systems', 6800.00, 3);

INSERT INTO Missions (MissionName, ScientistID, StartDate, EnergyConsumed, Status)
VALUES
('Black Hole Mapping', 1, GETDATE(), 12000.50, 'ACTIVE'),
('Alien Genome Study', 2, GETDATE(), 8000.40, 'ACTIVE');
# SCALAR FUNCTION (RISK INDEX)
CREATE FUNCTION dbo.CalculateRiskIndex
(
    @Radiation INT,
    @Gravity DECIMAL(5,2)
)
RETURNS FLOAT
AS
BEGIN
    RETURN (@Radiation * @Gravity) / 10.0;
END;
GO
# PLANET RISK OVERVIEW
CREATE VIEW vw_PlanetRisk AS
SELECT 
    PlanetName,
    RadiationLevel,
    GravityFactor,
    dbo.CalculateRiskIndex(RadiationLevel, GravityFactor) AS RiskIndex
FROM Planets;
GO
#
