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
# 
