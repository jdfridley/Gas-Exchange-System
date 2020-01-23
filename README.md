#A low-cost, automated, closed gas exchange system

This directory includes documentation for an automated closed gas exchange system for whole-plant C flux measurements, built at Syracuse University in 2018. 

**Citation**: Fridley et al. Metabolic scaling of roots and shoots: integrating power-law mass-flux relationships into studies of whole-plant carbon allocation (**forthcoming**).

Documentation includes:
- Summary description
- Design schematics
  - Top (shoot chambers and solenoids)
  - Bottom (root chambers and solenoids)
- Parts list and prices
- R control code


Flux system design. Our CO2 flux apparatus consists of 16 separate chambers connected to an infrared gas analyzer (LI-6262, LI-COR Inc., Lincoln, NE, USA). The system takes automated measurements of above- and belowground flux rates of 8 plants measured serially, using the rate of change of [CO2] (ppm s-1) in the airstream circulating in a closed system between a sealed chamber and the IRGA. Plants are arranged in two rows of four (Fig. X), each enclosed within a large (5106.3 mL) clear acrylic shoot chamber (TAP Plastics, Stockton, CA, USA) to the soil surface, and small (202.7 mL) PVC root chamber attached to the pot bottom. Prior to flux measurements, we watered (50 mL) pots and sealed pot soil surfaces with a combination of paraffin wax and petroleum jelly around plant stems to prevent air flow between shoot and root chambers. Shoot chambers are normally open to ambient air by means of a clear hinged top lid, and close upon measurement by means of a solenoid valve. Small 25 x 25 x 10 mm brushless fans facing upward are mounted on the side wall near the bottom of each shoot chamber to provide adequate air mixing, and a large box fan mounted at bench level provides air flow across all shoot chambers to facilitate mixing of the air inside and outside chambers, also preventing temperature increases. Root chambers are controlled by 3-way valves that normally pull ambient air through the chamber at 8 L min-1, which close to make an isolated loop between the IRGA and the chamber upon measurement. A network of CO2-impermeable vinyl tubing of 3.175 mm ID (Vinyl-Flex PVC NSF 61, Advanced Technology Products, Milford Center, Ohio, USA) connects all chambers to the IRGA at a flow rate of 3 L min-1 through a vacuum pump, and the chambers become serially isolated to the IRGA by activation of air input and output solenoids. Root chambers are sealed to pots with high-vacuum grease (Dow Corning, Midland, MI, USA), and aboveground gaskets are composed of EPDM rubber weatherstripping (MD Building Products, Oklahoma City, OK, USA). 
	Flux measurements are controlled by a computer running a control script in R 3.5.2 (R Core Team 2019). Lid and chamber valves are switched by a 32-channel relay board (Numato Systems Pvt. Ltd., Bengalaru, India), which is controlled by simple Python commands through USB-serial interface using the pyserial package (Liechti 2016). Our R control script invokes Python commands through the R “reticulate” package (Ushey et al. 2019), and monitors IRGA CO2 output once per second for a duration of 40 s per chamber by serial connection to the LI-6262. The R script also monitors temperature (C), relative humidity (%), and photosynthetic photon flux density at the top of shoot chambers each second by USB-serial interface to an an Arduino microcontroller attached to a SHT sensor (Sensiron, Staefa, Switzerland) and a SQ-110-SS quantum sensor (Apogee, Logan, UT, USA).

Flux calculations. We calculated absolute rates of plant root or shoot CO2 flux (𝝻mol CO2 s-1) at the time chambers were closed to ambient air as the product of air density (at 25 C and 50% RH), chamber volume, and the rate of change of CO2 in the chamber:

	CO2 flux (𝝻mol CO2 s-1) = [40.57 mol m-3 x (volume in m3)] x (ppm s-1 CO2)

The rate of change of chamber CO2 was calculated with ordinary least squares regression after allowing for full chamber mixing by excluding values over the first 20 s after chamber closing. The increase or decrease in chamber CO2 was almost perfectly linear (R2>0.99; see supplement) over the 20 s of measurement. In subsequent experiments we have included water dilution corrections as shoot cuvette humidity increases during the measurement (Licor Application Note 129); however, such corrections amount to <1% of CO2 flux values and so were ignored in the present study. 
For each plant, we used measurements of root and shoot fluxes taken approximately every 20 min to estimate Ashoot, Rn,shoot, Rd,root, and Rn,root. We modeled each of these flux components separately, using a generalized additive model (GAM) that included a spline smoother for time of day via the R ‘gam’ library (Hastie 2018; see supplement model code; fig. X). Before GAM analysis we removed outliers of flux rates that were greater than 1.5 SD from the overall mean, typically only one or two observations per model. We then used model predicted values to create a continuous function of root and shoot C flux for each second of 8 hrs of night (22:00 to 6:00) and 16 hrs of day (6:00 to 22:00). For the present study we use mean predicted values of Ashoot, Rn,shoot, Rd,root, and Rn,root for each diurnal measurement. To estimate total daily C gain, we integrated each flux measurement over its duration (16 hr daytime, 8 hr night), subtracted integrated Rn,shoot, Rd,root, and Rn,root from integrated Ashoot, and converted to g C (12/44 C:CO2 molecular mass ratio x 10-6 g ug-1 C). We also estimated root flux fraction as (Rd,root + Rn,root)/Ashoot.
