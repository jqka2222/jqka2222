SELECT
round(p.ra,6) as ra, round(p.dec,6) as dec,
p.run, --- comments are preceded by ---
round(p.extinction_r,3) as rExtSFD, --- r band extinction from SFD
round(p.modelMag_u,3) as uRaw, --- ISM-uncorrected model mags
round(p.modelMag_g,3) as gRaw, --- rounding up model magnitudes
round(p.modelMag_r,3) as rRaw,
round(p.modelMag_i,3) as iRaw,
round(p.modelMag_z,3) as zRaw,
round(p.modelMagErr_u,3) as uErr, --- errors are important!
round(p.modelMagErr_g,3) as gErr,
round(p.modelMagErr_r,3) as rErr,
round(p.modelMagErr_i,3) as iErr,
round(p.modelMagErr_z,3) as zErr,
round(p.psfMag_u,3) as uRawPSF, --- psf magnitudes
round(p.psfMag_g,3) as gRawPSF,
round(p.psfMag_r,3) as rRawPSF,
round(p.psfMag_i,3) as iRawPSF,
round(p.psfMag_z,3) as zRawPSF,
round(p.psfMagErr_u,3) as upsfErr,
round(p.psfMagErr_g,3) as gpsfErr,
round(p.psfMagErr_r,3) as rpsfErr,
round(p.psfMagErr_i,3) as ipsfErr,
round(p.psfMagErr_z,3) as zpsfErr,
p.type, --- tells if a source is resolved or not
(case when (p.flags & ’16’) = 0 then 1 else 0 end) as ISOLATED --- useful
INTO mydb.SDSSimagingSample
FROM PhotoTag p
WHERE
p.ra > 0.0 and p.ra < 10.0 and p.dec > -1 and p.dec < 1 --- 10x2 sq.deg.
and (p.type = 3 OR p.type = 6) and --- resolved and unresolved sources
(p.flags & ’4295229440’) = 0 and --- ’4295229440’ is magic code for no
--- DEBLENDED_AS_MOVING or SATURATED objects
p.mode = 1 and --- PRIMARY objects only, which implies
--- !BRIGHT && (!BLENDED || NODEBLEND || nchild == 0)]
p.modelMag_r < 22.5 --- adopted faint limit (same as about SDSS limit)
--- the end of query
