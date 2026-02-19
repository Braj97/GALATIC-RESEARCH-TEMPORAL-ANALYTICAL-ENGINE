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
# STORED PROCEDURE
CREATE PROCEDURE sp_GenerateEnergyReport
AS
BEGIN
    SELECT 
        M.MissionName,
        S.FullName,
        SUM(E.EnergyOutput) AS TotalEnergyGenerated,
        AVG(E.Temperature) AS AvgTemperature
    FROM Missions M
    JOIN Scientists S ON M.ScientistID = S.ScientistID
    JOIN EnergyLogs E ON M.MissionID = E.MissionID
    GROUP BY M.MissionName, S.FullName;
END;
GO
# TRIGGER (AI ANAMOLY DETECTOR)
CREATE TABLE AnomalyAlerts (
    AlertID INT PRIMARY KEY IDENTITY,
    MissionID INT,
    AlertMessage NVARCHAR(500),
    CreatedAt DATETIME DEFAULT GETDATE()
);
GO

CREATE TRIGGER trg_EnergyAnomaly
ON EnergyLogs
AFTER INSERT
AS
BEGIN
    INSERT INTO AnomalyAlerts (MissionID, AlertMessage)
    SELECT 
        i.MissionID,
        '⚠ Energy Spike Detected! Output exceeded safe threshold.'
    FROM inserted i
    WHERE i.EnergyOutput > 10000;
END;
GO
# INDEXING FOR PERFORMANCE
CREATE INDEX IX_EnergyLogs_MissionID
ON EnergyLogs(MissionID);

CREATE INDEX IX_Scientists_PlanetID
ON Scientists(AssignedPlanetID);
# COMPLEX QUERY
SELECT 
    M.MissionName,
    FORMAT(E.LogTime, 'yyyy-MM-dd HH') AS HourBlock,
    SUM(E.EnergyOutput) AS HourlyEnergy
FROM EnergyLogs E
JOIN Missions M ON E.MissionID = M.MissionID
GROUP BY M.MissionName, FORMAT(E.LogTime, 'yyyy-MM-dd HH')
ORDER BY HourBlock DESC;
# OUTPUT
https://i.supaimg.com/5908bf48-1440-41d6-b8cc-2a0fbc2648ec.png
