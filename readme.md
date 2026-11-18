# TWFC console commands

Identified 317 distinct reflected exec names (415 definitions), plus 145 native command roots and their options. There are 461 distinct root names in the combined static catalog. Analysis date: 2026-10-10.

## What is confirmed

Ghidra program: TWFC.exe, 32-bit x86, image base 0x10900000. Inspected all 2426 installed .xxx packages, including mods/A2_KON_BASE_m.xxx, with no parse failures. Validated 23,549 function records against their serialized script sizes and trailing flag/replication layout. Reflected commands are selected by FUNC_Exec (0x200), not by guessing from function names. Parameters come from CPF_Parm (0x80), excluding return values; optional parameters use CPF_OptionalParm (0x10).

The native UTF-16 parser at 0x116fff60 is case-insensitive. A raw scan found 208 direct calls to it, including six calls Ghidra had not disassembled. Twenty-six containing handlers were inspected; one remaining call parses the internal OnRelease input-binding modifier, which is not a root console command. Seven dynamic calls resolve to the SCALE, SHOW, and NXVIS tables listed below. The engine also has a conditional script-debugger fallback using case-sensitive prefixes.

Native coverage is exhaustive for direct calls to this identified parser and the inspected fallback. It does not prove that no other dispatcher or string-comparison implementation exists. Reflected coverage includes every Function export in the installed package set. Custom runtime handlers and dynamically supplied map event names may extend the accepted input.

## Availability and useful findings

This is a static command catalog, not a runtime-tested list. Exec flags confirm eligibility for reflected console dispatch; the owning object must exist and receive the command. Cheat-manager creation, debug-manager routing, current controller state, network authority, and build restrictions can prevent execution. State overrides may intentionally consume a command without acting. Empty script bodies are explicitly marked; absence of an empty body does not prove the command has an effect.

- Implemented script bodies exist for God, PseudoGod, SloMo, UnlimitedAmmo, Teleport, ToggleDebugCamera, SkipToCheckpoint, and many game-specific debug commands.
- Base CheatManager.AllAmmo and CheatManager.AllWeapons have empty script bodies. UnlimitedAmmo is the separate HmCheatManager implementation.
- No reflected Fly, Ghost, or Walk exec exports were found in any inspected package. A string named ghost elsewhere in the executable is insufficient evidence for a console command.
- RECORDINPUT checks the benchmark-mode flag; the inspected branches only accept subcommands and do not expose a recording implementation here.
- Native diagnostic commands include deliberate no-op or stub paths. Availability must be checked in the running build.
- DebugFinishCampaign occurs in several map packages; it is not a new distinct command in each map.
- Misspellings such as ReloadCharacerUI are preserved exactly as exported.

## Selected game commands

| Syntax | Owner | Body |
|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | non-empty script body |
| `Fov <F:float>` | `PlayerController.Fov` | non-empty script body |
| `AllAmmo` | `CheatManager.AllAmmo` | empty script body |
| `God` | `CheatManager.God` | non-empty script body |
| `SloMo <T:float>` | `CheatManager.SloMo` | non-empty script body |
| `AllWeapons` | `CheatManager.AllWeapons` | empty script body |
| `UnlimitedAmmo` | `HmCheatManager.UnlimitedAmmo` | non-empty script body |
| `DebugGiveSkill <SkillUniqueId:string> <Level:int>` | `TnPawn.DebugGiveSkill` | non-empty script body |
| `DebugGiveAbility <AbilityUniqueId:string>` | `TnPawn.DebugGiveAbility` | non-empty script body |
| `SkipToCheckpoint <CheckpointName:string>` | `TnCheatManager.SkipToCheckpoint` | non-empty script body |
| `AddXp <Specialty:string> [Amount:float]` | `TnDebugManager.AddXp` | non-empty script body |
| `DebugGodMode` | `TnDebugManager.DebugGodMode` | non-empty script body |
| `LogCheckpoints` | `TnDebugManager.LogCheckpoints` | non-empty script body |
| `DebugCompleteMission <MapId:int> [Difficulty:int]` | `TnDebugManager.DebugCompleteMission` | non-empty script body |
| `DebugUnlockCharacter <ChassisID:string>` | `TnDebugManager.DebugUnlockCharacter` | non-empty script body |
| `DebugUnlockPrimeMode` | `TnDebugManager.DebugUnlockPrimeMode` | non-empty script body |

## Native commands and options

Angle brackets denote arguments; square brackets denote optional arguments. Syntax details are supplied where the parser was inspected; a bare token may still require arguments. Options are shown with their parent command. SCALE boolean values are matched against TRUE; other tokens evaluate false in that branch. SCALE integer and floating-point settings use numeric conversion.

### ProcessViewportInputCommands — 0x10c4ae50

| Syntax/token | Evidence |
|---|---|
| `RECORDINPUT` | call 0x10c4ae89, string 0x128c016c |
| `RECORDINPUT START` | call 0x10c4aebd, string 0x128c01ec |
| `RECORDINPUT STOP` | call 0x10c4aed3, string 0x128c01f8 |
| `RECORDINPUT PLAYUNTILDONE` | call 0x10c4aee9, string 0x128c0204 |
| `RECORDINPUT PLAY` | call 0x10c4aeff, string 0x128c0220 |
| `RESETGFXVIEWPORT` | call 0x10c4af1a, string 0x128c022c |

### ProcessScaleformCommands — 0x10c4c710

| Syntax/token | Evidence |
|---|---|
| `OPENSCENE` | call 0x10c4c72b, string 0x128c0400 |
| `CLOSESCENE` | call 0x10c4c7fe, string 0x128c0414 |
| `CAPTUREINPUT` | call 0x10c4c835, string 0x128c042c |
| `RELEASEINPUT` | call 0x10c4c874, string 0x128c0448 |
| `DUMPSFTEXTURES` | call 0x10c4c93c, string 0x128c0464 |

### ProcessAllocationDumpCommand — 0x1100dcd0

| Syntax/token | Evidence |
|---|---|
| `DUMPALLOCS [SUMMARYONLY]` | call 0x1100dcdd, string 0x12901be0 |

### ProcessPathBuildingCommands — 0x114419d0

| Syntax/token | Evidence |
|---|---|
| `DEFINEPATHS` | call 0x114419e9, string 0x129a0fac |
| `SETPATHCOLLISION` | call 0x11441a7d, string 0x129a0ffc |

### ProcessOctreeCommands — 0x11441ae0

| Syntax/token | Evidence |
|---|---|
| `SHOWOCTREE` | call 0x11441af0, string 0x129a0dc4 |
| `ANALYZEOCTREE` | call 0x11441b1e, string 0x129a0ddc |
| `OCTREESUMMARY` | call 0x11441b8f, string 0x129a0df8 |
| `TRIMOCTREE` | call 0x11441c00, string 0x129a0e14 |

### ProcessNavigationOctreeCommands — 0x11458540

| Syntax/token | Evidence |
|---|---|
| `NAVOCTREE` | call 0x1145854e, string 0x129a1058 |
| `NAVOCTREE STATS` | call 0x11458568, string 0x129a106c |
| `NAVOCTREE FIND` | call 0x1145860a, string 0x129a1188 |
| `NAVOCTREE DUMP` | call 0x11458646, string 0x129764e8 |

### ProcessDiagnosticCommands — 0x11476b80

| Syntax/token | Evidence |
|---|---|
| `LISTTEXTURES` | call 0x11476bbd, string 0x129a1248 |
| `LISTTEXTURES STREAMING` | call 0x11476bd7, string 0x129a1264 |
| `LISTTEXTURES NONSTREAMING` | call 0x11476bed, string 0x129a1278 |
| `LISTANIMSETS` | call 0x11476f88, string 0x129a12a8 |
| `ANIMSEQSTATS` | call 0x1147711b, string 0x129a12c4 |
| `SHOWHOTKISMET` | call 0x11477156, string 0x129a12e0 |
| `DLE` | call 0x114772fc, string 0x129a136c |
| `DN` | call 0x1147745a, string 0x129a1438 |
| `SHOWSKELCOMPTICKTIME` | call 0x11477474, string 0x129a1440 |
| `LISTSKELMESHES` | call 0x114774ab, string 0x129a146c |
| `LISTPAWNCOMPONENTS` | call 0x11477634, string 0x129a148c |
| `TOGGLECROWDS` | call 0x11477690, string 0x129a14b4 |
| `EXEC <filename>` | call 0x114776d8, string 0x129a14d0 |
| `TOGGLEDRAWEVENTS` | call 0x1147773a, string 0x129a14dc |
| `PUSHVIEW` | call 0x1147777c, string 0x129a1500 |
| `PUSHVIEW START` | call 0x11477796, string 0x128c01ec |
| `PUSHVIEW STOP` | call 0x114777d2, string 0x128c01f8 |
| `PUSHVIEW SYNC` | call 0x1147780a, string 0x129a1514 |
| `CANCELMATINEE` | call 0x114779d6, string 0x129a1520 |
| `BloomScale` | call 0x11477a21, string 0x129a153c |
| `SceneShadows` | call 0x11477a6b, string 0x129a1558 |
| `SceneMidtones` | call 0x11477acd, string 0x129a1574 |
| `SceneHighlights` | call 0x11477b2f, string 0x129a1590 |
| `Desaturation` | call 0x11477b99, string 0x1290ee18 |
| `MaxFarBlur` | call 0x11477be5, string 0x129a15b0 |
| `MaxNearBlur` | call 0x11477c2f, string 0x129a15c8 |
| `FocusDistance` | call 0x11477c79, string 0x129a15e0 |
p
### ProcessChartCommands — 0x11490c80

| Syntax/token | Evidence |
|---|---|
| `CHART` | call 0x11490c95, string 0x1298f0ac |
| `CHART SHOW` | call 0x11490caf, string 0x1295d2a8 |
| `CHART KEY` | call 0x11490cdb, string 0x1298f0b8 |
| `CHART LOCKSCALE` | call 0x11490d09, string 0x1298f0c0 |
| `CHART RESCALE` | call 0x11490d37, string 0x1298f0d4 |
| `CHART RESET` | call 0x11490d5f, string 0x1297660c |

### ProcessTextureStreamingCommand — 0x114e8a70

| Syntax/token | Evidence |
|---|---|
| `DISABLEFORCEMIP` | call 0x114e8b2d, string 0x129770ec |

### ProcessSoundCommands — 0x114fb6e0

| Syntax/token | Evidence |
|---|---|
| `ListSounds` | call 0x114fb6f5, string 0x12979520 |
| `ListSoundDurations` | call 0x114fb72c, string 0x12979538 |

### ProcessScalabilityCommands — 0x11513ab0

| Syntax/token | Evidence |
|---|---|
| `SCALE` | call 0x11513e96, string 0x129764dc |
| `SCALE DUMP` | call 0x11513eb4, string 0x129764e8 |
| `SCALE DUMPINI` | call 0x11513ec9, string 0x12976520 |
| `SCALE LEVEL <1..5>` | call 0x11513f1d, string 0x12976574 |
| `SCALE LOWEND` | call 0x11514019, string 0x129765ec |
| `SCALE HIGHEND` | call 0x11514136, string 0x129765fc |
| `SCALE RESET` | call 0x1151429a, string 0x1297660c |
| `SCALE SET` | call 0x115142c3, string 0x1290f7a8 |
| `SCALE TOGGLE` | call 0x11514391, string 0x1294c3c8 |
| `SCALE SET StaticDecals <value>` | local setting table |
| `SCALE TOGGLE StaticDecals` | local boolean table |
| `SCALE SET DynamicDecals <value>` | local setting table |
| `SCALE TOGGLE DynamicDecals` | local boolean table |
| `SCALE SET DynamicLights <value>` | local setting table |
| `SCALE TOGGLE DynamicLights` | local boolean table |
| `SCALE SET DynamicShadows <value>` | local setting table |
| `SCALE TOGGLE DynamicShadows` | local boolean table |
| `SCALE SET LightEnvironmentShadows <value>` | local setting table |
| `SCALE TOGGLE LightEnvironmentShadows` | local boolean table |
| `SCALE SET CompositeDynamicLights <value>` | local setting table |
| `SCALE TOGGLE CompositeDynamicLights` | local boolean table |
| `SCALE SET MotionBlur <value>` | local setting table |
| `SCALE TOGGLE MotionBlur` | local boolean table |
| `SCALE SET AccumulationBuffer <value>` | local setting table |
| `SCALE TOGGLE AccumulationBuffer` | local boolean table |
| `SCALE SET DepthOfField <value>` | local setting table |
| `SCALE TOGGLE DepthOfField` | local boolean table |
| `SCALE SET AmbientOcclusion <value>` | local setting table |
| `SCALE TOGGLE AmbientOcclusion` | local boolean table |
| `SCALE SET Bloom <value>` | local setting table |
| `SCALE TOGGLE Bloom` | local boolean table |
| `SCALE SET QualityBloom <value>` | local setting table |
| `SCALE TOGGLE QualityBloom` | local boolean table |
| `SCALE SET Distortion <value>` | local setting table |
| `SCALE TOGGLE Distortion` | local boolean table |
| `SCALE SET DropParticleDistortion <value>` | local setting table |
| `SCALE TOGGLE DropParticleDistortion` | local boolean table |
| `SCALE SET SpeedTreeLeaves <value>` | local setting table |
| `SCALE TOGGLE SpeedTreeLeaves` | local boolean table |
| `SCALE SET SpeedTreeFronds <value>` | local setting table |
| `SCALE TOGGLE SpeedTreeFronds` | local boolean table |
| `SCALE SET LensFlares <value>` | local setting table |
| `SCALE TOGGLE LensFlares` | local boolean table |
| `SCALE SET FogVolumes <value>` | local setting table |
| `SCALE TOGGLE FogVolumes` | local boolean table |
| `SCALE SET FloatingPointRenderTargets <value>` | local setting table |
| `SCALE TOGGLE FloatingPointRenderTargets` | local boolean table |
| `SCALE SET Trilinear <value>` | local setting table |
| `SCALE TOGGLE Trilinear` | local boolean table |
| `SCALE SET OneFrameThreadLag <value>` | local setting table |
| `SCALE TOGGLE OneFrameThreadLag` | local boolean table |
| `SCALE SET UseVsync <value>` | local setting table |
| `SCALE TOGGLE UseVsync` | local boolean table |
| `SCALE SET UpscaleScreenPercentage <value>` | local setting table |
| `SCALE TOGGLE UpscaleScreenPercentage` | local boolean table |
| `SCALE SET Fullscreen <value>` | local setting table |
| `SCALE TOGGLE Fullscreen` | local boolean table |
| `SCALE SET AllowD3D10 <value>` | local setting table |
| `SCALE TOGGLE AllowD3D10` | local boolean table |
| `SCALE SET EnableHighPolyChars <value>` | local setting table |
| `SCALE TOGGLE EnableHighPolyChars` | local boolean table |
| `SCALE SET UseSimpleUpscaling <value>` | local setting table |
| `SCALE TOGGLE UseSimpleUpscaling` | local boolean table |
| `SCALE SET SkeletalMeshLODBias <value>` | local setting table |
| `SCALE SET ParticleLODBias <value>` | local setting table |
| `SCALE SET DetailMode <value>` | local setting table |
| `SCALE SET ShadowFilterQualityBias <value>` | local setting table |
| `SCALE SET MaxAnisotropy <value>` | local setting table |
| `SCALE SET MaxMultisamples <value>` | local setting table |
| `SCALE SET MinShadowResolution <value>` | local setting table |
| `SCALE SET MaxShadowResolution <value>` | local setting table |
| `SCALE SET ResX <value>` | local setting table |
| `SCALE SET ResY <value>` | local setting table |
| `SCALE SET HighDef3DResX <value>` | local setting table |
| `SCALE SET HighDef3DResY <value>` | local setting table |
| `SCALE SET ScreenPercentageX <value>` | local setting table |
| `SCALE SET ScreenPercentageY <value>` | local setting table |
| `SCALE SET SceneCaptureStreamingMultiplier <value>` | local setting table |
| `SCALE SET FoliageDrawRadiusMultiplier <value>` | local setting table |
| `SCALE SET ShadowTexelsPerPixel <value>` | local setting table |
| `SCALE SET FractureCullDistanceScale <value>` | local setting table |
| `SCALE SET UpscaleSharpness <value>` | local setting table |

### ProcessLightEnvironmentCommand — 0x11549d60

| Syntax/token | Evidence |
|---|---|
| `DirectLightEnv` | call 0x11549d7b, string 0x1296c214 |

### ProcessCollisionDebugCommands — 0x11587090

| Syntax/token | Evidence |
|---|---|
| `SHOWEXTENTLINECHECK` | call 0x115870c6, string 0x12967348 |
| `SHOWLINECHECK` | call 0x115870f9, string 0x12967370 |
| `SHOWPOINTCHECK` | call 0x1158712c, string 0x1296738c |
| `FLUSHPERSISTENTPRIMITIVES` | call 0x1158715f, string 0x129673ac |

### ProcessViewportCommands — 0x115c7040

| Syntax/token | Evidence |
|---|---|
| `SHOW` | call 0x115c705a, string 0x1295d2a8 |
| `SHOW BONES` | call 0x115c751f, string 0x1295d6cc |
| `SHOW PREPHYSBONES` | call 0x115c7551, string 0x1295d6d8 |
| `VIEWMODE` | call 0x115c7b09, string 0x1295d7e0 |
| `VIEWMODE WIREFRAME` | call 0x115c7bc3, string 0x1295d7f4 |
| `VIEWMODE BRUSHWIREFRAME` | call 0x115c7beb, string 0x1295d808 |
| `VIEWMODE UNLIT` | call 0x115c7c29, string 0x1295d828 |
| `VIEWMODE LIGHTINGONLY` | call 0x115c7c65, string 0x1295d834 |
| `VIEWMODE LIGHTCOMPLEXITY` | call 0x115c7c8a, string 0x1295d850 |
| `VIEWMODE SHADERCOMPLEXITY` | call 0x115c7caf, string 0x1295c968 |
| `VIEWMODE TEXTUREDENSITY` | call 0x115c7cd4, string 0x1295c948 |
| `SETSHOWFLAGS` | call 0x115c7d1c, string 0x1295d870 |
| `NEXTVIEWMODE` | call 0x115c7d5d, string 0x1295d8c0 |
| `PREVVIEWMODE` | call 0x115c7f25, string 0x1295d8dc |
| `hiderebuildlighting` | call 0x115c80f0, string 0x1295d8f8 |
| `hidetexturelimitreached` | call 0x115c8119, string 0x1295d920 |
| `DUMPDYNAMICSHADOWSTATS` | call 0x115c8142, string 0x1295d950 |
| `PRECACHE` | call 0x115c8171, string 0x1295d980 |
| `SETRES <width>x<height>[f|w]` | call 0x115c819f, string 0x1295d994 |
| `TILEDSHOT` | call 0x115c82a7, string 0x1295d9a4 |
| `SHOT` | call 0x115c82d9, string 0x1295d9b8 |
| `SCREENSHOT` | call 0x115c82f2, string 0x1295d9c4 |
| `BUGSCREENSHOT` | call 0x115c830b, string 0x1295d9dc |
| `CLUTSHOT` | call 0x115c836d, string 0x1295da14 |
| `CLUT` | call 0x115c83a4, string 0x1295da28 |
| `DRAWCLUT` | call 0x115c83bd, string 0x1295da34 |
| `KILLPARTICLES` | call 0x115c83d6, string 0x1295da48 |
| `TOGGLEUI` | call 0x115c846f, string 0x1295da64 |
| `ShowParticleLOD` | call 0x115c84a2, string 0x1295da78 |
| `DrawInvisibleParticles` | call 0x115c84ee, string 0x1295da98 |
| `SHOW BOUNDS` | local option table |
| `SHOW BSPSPLIT` | local option table |
| `SHOW CAMFRUSTUMS` | local option table |
| `SHOW COLLISION` | local option table |
| `SHOW GROUND` | local option table |
| `SHOW CONSTRAINTS` | local option table |
| `SHOW DECALINFO` | local option table |
| `SHOW DECALS` | local option table |
| `SHOW DYNAMICSHADOWS` | local option table |
| `SHOW FOLIAGE` | local option table |
| `SHOW HITPROXIES` | local option table |
| `SHOW LENSFLARES` | local option table |
| `SHOW LEVELCOLORATION` | local option table |
| `SHOW MESHEDGES` | local option table |
| `SHOW MISSINGCOLLISION` | local option table |
| `SHOW NAVNODES` | local option table |
| `SHOW NONZEROEXTENT` | local option table |
| `SHOW PARTICLES` | local option table |
| `SHOW PATHS` | local option table |
| `SHOW PORTALS` | local option table |
| `SHOW POSTPROCESS` | local option table |
| `SHOW RIGIDBODY` | local option table |
| `SHOW SCENECAPTURE` | local option table |
| `SHOW SHADOWFRUSTUMS` | local option table |
| `SHOW SKELETALMESHES` | local option table |
| `SHOW SKELMESHES` | local option table |
| `SHOW SPEEDTREES` | local option table |
| `SHOW SPRITES` | local option table |
| `SHOW STATICMESHES` | local option table |
| `SHOW TERRAIN` | local option table |
| `SHOW TERRAINCOLLISION` | local option table |
| `SHOW TERRAINPATCHES` | local option table |
| `SHOW TEXTUREDENSITY` | local option table |
| `SHOW UNLITTRANSLUCENCY` | local option table |
| `SHOW ZEROEXTENT` | local option table |
| `SHOW VOLUMES` | local option table |
| `SHOW SHADOWVOLUMES` | local option table |
| `SHOW ANIMATEDSKEL` | local option table |
| `SHOW INTERACT` | local option table |
| `SHOW OCCLUDEDWIREFRAME` | local option table |
| `SHOW OVERDRAWCOLORATION` | local option table |
| `SHOW LEVELMARKUP` | local option table |
| `SHOW ROOMPORTALS` | local option table |
| `SHOW PARTICLECOST` | local option table |
| `SHOW BSP` | local option table |
| `SHOW FOG` | local option table |
| `VIEWMODE LIT` | default viewmode branch; no literal LIT match |

### ProcessEngineCommands — 0x115cb680

| Syntax/token | Evidence |
|---|---|
| `SHOWLOG` | call 0x115cb76c, string 0x1295c6e8 |
| `GAMEVER` | call 0x115cb7b5, string 0x1295c6f8 |
| `GAMEVERSION` | call 0x115cb7ce, string 0x1295c708 |
| `CRACKURL` | call 0x115cb7e7, string 0x1295c780 |
| `DEFER <command>` | call 0x115cb972, string 0x1295c8cc |
| `TOGGLEBPCF` | call 0x115cb9b7, string 0x1295c8d8 |
| `SHADOWRADIUS` | call 0x115cbaf1, string 0x1295c8f0 |
| `SHADOWDEPTHBIAS` | call 0x115cbb45, string 0x1295c90c |
| `RESETFPSCHART` | call 0x115cbb99, string 0x1295c92c |
| `TEXTUREDENSITY` | call 0x115cbbce, string 0x1295c948 |
| `SHADERCOMPLEXITY` | call 0x115cbd0c, string 0x1295c968 |
| `TOGGLECOLLISIONOVERLAY` | call 0x115cbec3, string 0x1295cafc |
| `LISTMISSINGPHYSICALMATERIALS` | call 0x115cbf05, string 0x1295cb2c |
| `FULLMOTIONBLUR [-1|0|1]` | call 0x115cbfd7, string 0x1295cb68 |
| `FLUSHIOMANAGER` | call 0x115cc083, string 0x1295cb88 |
| `FlushAsyncLoading` | call 0x115cc0ab, string 0x1295cba8 |
| `AVAILABLETEXMEM` | call 0x115cc0db, string 0x1295cbcc |
| `DUMPAVAILABLERESOLUTIONS` | call 0x115cc103, string 0x1295cbec |
| `LowResParticles <0|1|2>` | call 0x115cc153, string 0x1295cc20 |
| `LightEnvironmentLightCount <1|2|3> [perf]` | call 0x115cc1ef, string 0x1295cc40 |
| `ReattachLightEnvironments` | call 0x115cc365, string 0x1295cc84 |
| `ParticlePerfMode` | call 0x115cc39b, string 0x1295ccb8 |
| `ParticlePerfTimers` | call 0x115cc3f2, string 0x1295ccdc |
| `STAT` | call 0x115cc415, string 0x1290f528 |
| `STAT TOGGLEINGAME` | call 0x115cc436, string 0x1295cd04 |
| `STAT TOGGLEINEDITOR` | call 0x115cc46b, string 0x1295cd20 |
| `STAT FPS` | call 0x115cc4ee, string 0x129597c4 |
| `STAT FORCEHIDEFPS` | call 0x115cc518, string 0x1295cd40 |
| `STAT ONLINEASYNCTASKS` | call 0x115cc542, string 0x1295cd5c |
| `STAT LEVELS` | call 0x115cc577, string 0x1295cd80 |
| `STAT LEVELS PRESTREAMING` | call 0x115cc591, string 0x1295cd90 |
| `STAT LEVELS ALL` | call 0x115cc5c6, string 0x1295cdac |
| `STAT UNIT` | call 0x115cc62c, string 0x1295cdb4 |
| `ToggleVSync` | call 0x115cc660, string 0x1295cdc0 |
| `SHADERCOMPLEXITY [TOGGLEADDITIVE|TOGGLEPIXEL|MAX <integer>]` | secondary _wcsicmp comparisons |

### ProcessPhysicsCommands — 0x115fdcc0

| Syntax/token | Evidence |
|---|---|
| `NXSTATS` | call 0x115fdcd8, string 0x1294b940 |
| `NXMESHSCALES` | call 0x115fdcfe, string 0x1294b950 |
| `NXDUMPMEM` | call 0x115fdef6, string 0x1294b9f0 |
| `NXVRD` | call 0x115fdf17, string 0x1294ba04 |
| `NXVRD CONNECT [host]` | call 0x115fdf8a, string 0x1294ba10 |
| `NXVRD DISCONNECT` | call 0x115fe04d, string 0x12939258 |
| `NXVIS` | call 0x115fe077, string 0x1294ba2c |
| `NXDUMPAWAKE` | call 0x115fe428, string 0x1294bce4 |
| `NXVIS WORLDAXES` | local option table |
| `NXVIS BODYAXES` | local option table |
| `NXVIS MASSAXES` | local option table |
| `NXVIS CONTACTPOINT` | local option table |
| `NXVIS CONTACTS` | local option table |
| `NXVIS CONTACTERROR` | local option table |
| `NXVIS CONTACTFORCE` | local option table |
| `NXVIS JOINTLIMITS` | local option table |
| `NXVIS JOINTLOCALAXES` | local option table |
| `NXVIS JOINTWORLDAXES` | local option table |
| `NXVIS CCDTESTS` | local option table |
| `NXVIS COLLISIONAABBS` | local option table |
| `NXVIS COLLISION` | local option table |
| `NXVIS COLLISIONAXES` | local option table |
| `NXVIS CLOTH_MESH` | local option table |
| `NXVIS CLOTH_COLLISIONS` | local option table |
| `NXVIS CLOTH_ATTACHMENT` | local option table |
| `NXVIS CLOTH_SLEEP` | local option table |
| `NXVIS FORCEFIELDS` | local option table |
| `NXVIS CCD` | local option table |

### ProcessInputBindingCommands — 0x116037e0

| Syntax/token | Evidence |
|---|---|
| `BUTTON` | call 0x11603813, string 0x1294c384 |
| `PULSE` | call 0x116038ba, string 0x1294c3bc |
| `TOGGLE` | call 0x1160390a, string 0x1294c3c8 |
| `AXIS` | call 0x1160399e, string 0x1294c400 |
| `COUNT` | call 0x11603b9e, string 0x1294c484 |
| `KEYBINDING <key>` | call 0x11603c13, string 0x1294c4bc |

### ProcessNetworkDebugCommands — 0x11629950

| Syntax/token | Evidence |
|---|---|
| `SOCKETS` | call 0x11629967, string 0x129480a8 |
| `PACKAGEMAP` | call 0x11629bb5, string 0x12948138 |

### ProcessGammaCommand — 0x116811c0

| Syntax/token | Evidence |
|---|---|
| `GAMMA <float>` | call 0x116811d7, string 0x12929418 |

### ProcessGameEngineCommands — 0x1169d890

| Syntax/token | Evidence |
|---|---|
| `MEMTAG_UPDATE` | call 0x1169d8b6, string 0x129391a8 |
| `OPEN <map-or-URL>` | call 0x1169d8d0, string 0x129391c4 |
| `START <map-or-URL>` | call 0x1169da50, string 0x128c01ec |
| `SERVERTRAVEL <map-or-URL>` | call 0x1169dbbb, string 0x12939234 |
| `SAY` | call 0x1169dc2a, string 0x12939250 |
| `DISCONNECT` | call 0x1169dc86, string 0x12939258 |
| `DISCONNECT LOCAL` | call 0x1169dd5e, string 0x12939288 |
| `RECONNECT` | call 0x1169de85, string 0x129392f8 |
| `RELOADMAP` | call 0x1169df08, string 0x1293930c |
| `EXIT` | call 0x1169e01a, string 0x1290d420 |
| `QUIT` | call 0x1169e033, string 0x1290d42c |
| `GETMAXTICKRATE` | call 0x1169e04d, string 0x12939334 |
| `SetMaxTickRate Rate=<integer>` | call 0x1169e094, string 0x12939354 |
| `CANCEL` | call 0x1169e0ed, string 0x12939380 |
| `ListPrecacheMapPackages` | call 0x1169e223, string 0x129393b4 |
| `ListLoadedPackages` | call 0x1169e2d7, string 0x12939444 |
| `SetEditorViewport` | call 0x1169e42e, string 0x129394bc |
| `SET <Class> <Property> <Value>` | call 0x1169e4a4, string 0x1290f7a8 |
| `SETNOPEC <Class> <Property> <Value>` | call 0x1169e4ba, string 0x1290f7b0 |

### ProcessCoreCommands — 0x116cc9b0

| Syntax/token | Evidence |
|---|---|
| `CONFIGHASH` | call 0x116cc9ba, string 0x1290d3f4 |
| `CONFIGMEM` | call 0x116cc9ed, string 0x1290d40c |
| `EXIT` | call 0x116cca20, string 0x1290d420 |
| `QUIT` | call 0x116cca36, string 0x1290d42c |

### ProcessObjectCommands — 0x116f6d00

| Syntax/token | Evidence |
|---|---|
| `MEM [STAT|DETAILED]` | call 0x116f6d26, string 0x1290f520 |
| `SETTRACKINGBASELINE` | call 0x116f6eef, string 0x1290f6c8 |
| `GET <Class> <Property>` | call 0x116f6f46, string 0x1290f6f0 |
| `GETALL <Class> <Property> [SHOWDEFAULTS] [SHOWPENDINGKILLS]` | call 0x116f708c, string 0x1290f6f8 |
| `GETALL <Class> <Property> SHOWDEFAULTS` | call 0x116f714d, string 0x1290f718 |
| `GETALL <Class> <Property> SHOWPENDINGKILLS` | call 0x116f7160, string 0x1290253c |
| `SET <Class> <Property> <Value>` | call 0x116f7561, string 0x1290f7a8 |
| `SETNOPEC <Class> <Property> <Value>` | call 0x116f7598, string 0x1290f7b0 |
| `VERIFYCOMPONENTS` | call 0x116f75cf, string 0x1290f7c4 |
| `SUPPRESS` | call 0x116f7821, string 0x1290f85c |
| `UNSUPPRESS` | call 0x116f78a8, string 0x1290f870 |
| `AlwaysFlushConsoleOutput` | call 0x116f7961, string 0x1290f888 |

### ProcessTaggedAllocationDumpCommand — 0x11708070

| Syntax/token | Evidence |
|---|---|
| `DUMPALLOCS [SUMMARYONLY]` | call 0x11708087, string 0x12901be0 |

### ProcessAllocatorCommands — 0x1170d170

| Syntax/token | Evidence |
|---|---|
| `DUMPALLOCSTOFILE` | call 0x1170d183, string 0x129019d4 |
| `STARTTRACKING` | call 0x1170d1b3, string 0x129019f8 |
| `STOPTRACKING` | call 0x1170d21c, string 0x12901a54 |
| `TAGMEMORY` | call 0x1170d269, string 0x12901aac |
| `TAGMEMORY START <0..31>` | call 0x1170d2a7, string 0x128c01ec |
| `TAGMEMORY STOP <0..31>` | call 0x1170d2e0, string 0x128c01f8 |

### ProcessObjectStateCommand — 0x1172b790

| Syntax/token | Evidence |
|---|---|
| `GETALLSTATE <Class> [SHOWPENDINGKILLS]` | call 0x1172b7b6, string 0x12902524 |
| `GETALLSTATE <Class> SHOWPENDINGKILLS` | call 0x1172b816, string 0x1290253c |

### ProcessScriptDebuggerCommands — 0x117dc1b0

| Syntax/token | Evidence |
|---|---|
| `trace <value>` | case-sensitive prefix comparison; gated fallback |
| `disassemble <argument>` | case-sensitive prefix comparison; gated fallback |

### ProcessWindowsClientCommands — 0x117fb270

| Syntax/token | Evidence |
|---|---|
| `PARANOIDDEVICELOSTCHECKING` | call 0x117fb293, string 0x128c49b4 |
| `USENEWMOUSEINPUT` | call 0x117fb2d1, string 0x128c4a2c |

## Reflected commands: complete signatures and definitions

Package offsets below refer to the decompressed logical package, not compressed on-disk offsets. Duplicate names preserve different owners and controller states. `<Class>`/`<Object>` types denote a class or object parameter; the precise referenced subtype is retained only in the original export record, not resolved in this report.

### A1_IAC_Base_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x1e1cfa` | non-empty |

### A2_KON_BASE_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x1f1a58` | non-empty |

### A3_COR_Base_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x1e28d7` | non-empty |

### A4_ORB_Base_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x1dfa9b` | non-empty |

### A5_TRY_BASE_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x256845` | non-empty |

### D1_ORB_Base_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x1eda1b` | non-empty |

### D2_COR_Base_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x1d832b` | non-empty |

### D3A_IAC_Base_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x1f67ad` | non-empty |

### D3B_IAC_Base_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x23115c` | non-empty |

### D5_OME_BASE_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x239a19` | non-empty |

### Engine.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `Admin <CommandLine:string>` | `Admin.Admin` | `0x40202` | `0x2191b1` | non-empty |
| `Kick <S:string>` | `Admin.Kick` | `0x40202` | `0x2193e3` | non-empty |
| `KickBan <S:string>` | `Admin.KickBan` | `0x40202` | `0x2192e9` | non-empty |
| `PlayerList` | `Admin.PlayerList` | `0x40202` | `0x2194e1` | non-empty |
| `RestartMap` | `Admin.RestartMap` | `0x40202` | `0x21959a` | non-empty |
| `Switch <URL:string>` | `Admin.Switch` | `0x40202` | `0x21965a` | non-empty |
| `AffectedByHitEffects` | `CheatManager.AffectedByHitEffects` | `0x40202` | `0x255897` | non-empty |
| `AllAmmo` | `CheatManager.AllAmmo` | `0x40200` | `0x2557b2` | empty |
| `AllWeapons` | `CheatManager.AllWeapons` | `0x40200` | `0x25700b` | empty |
| `Avatar <ClassName:name>` | `CheatManager.Avatar` | `0x40202` | `0x25613d` | non-empty |
| `BugIt [ScreenShotDescription:string]` | `CheatManager.BugIt` | `0x840202` | `0x255357` | non-empty |
| `BugItGo <X:float> <Y:float> <Z:float> <Pitch:int> <Yaw:int> <Roll:int>` | `CheatManager.BugItGo` | `0x840202` | `0x255046` | non-empty |
| `ChangeSize <F:float>` | `CheatManager.ChangeSize` | `0x40202` | `0x2556c4` | non-empty |
| `FreezeFrame <Delay:float>` | `CheatManager.FreezeFrame` | `0x40202` | `0x254b44` | non-empty |
| `GiveWeapon <WeaponClassStr:string>` | `CheatManager.GiveWeapon` | `0x20202` | `0x25658e` | non-empty |
| `God` | `CheatManager.God` | `0x40202` | `0x2557ec` | non-empty |
| `KillAll <aClass:Class>` | `CheatManager.KillAll` | `0x40202` | `0x255cfd` | non-empty |
| `KillPawns` | `CheatManager.KillPawns` | `0x40202` | `0x255f9c` | non-empty |
| `KillViewedActor` | `CheatManager.KillViewedActor` | `0x40202` | `0x254c5b` | non-empty |
| `ListDynamicActors` | `CheatManager.ListDynamicActors` | `0x40202` | `0x254a7e` | non-empty |
| `Loaded` | `CheatManager.Loaded` | `0x40202` | `0x256fa3` | non-empty |
| `PlayersOnly` | `CheatManager.PlayersOnly` | `0x40202` | `0x25664e` | non-empty |
| `PseudoGod` | `CheatManager.PseudoGod` | `0x40202` | `0x255956` | non-empty |
| `RememberSpot` | `CheatManager.RememberSpot` | `0x40202` | `0x2566b7` | non-empty |
| `SetGravity <F:float>` | `CheatManager.SetGravity` | `0x40202` | `0x255b4a` | non-empty |
| `SetJumpZ <F:float>` | `CheatManager.SetJumpZ` | `0x40202` | `0x255ac9` | non-empty |
| `SetSpeed <F:float>` | `CheatManager.SetSpeed` | `0x40202` | `0x255bcb` | non-empty |
| `SloMo <T:float>` | `CheatManager.SloMo` | `0x20202` | `0x255a3b` | non-empty |
| `Summon <ClassName:string> [Distance:float]` | `CheatManager.Summon` | `0x840202` | `0x2563a9` | non-empty |
| `Teleport` | `CheatManager.Teleport` | `0x840202` | `0x254e17` | non-empty |
| `TimeStepDisable` | `CheatManager.TimeStepDisable` | `0x40202` | `0x2548e5` | non-empty |
| `TimeStepRun` | `CheatManager.TimeStepRun` | `0x40202` | `0x254937` | non-empty |
| `TimeStepSeconds <Seconds:float>` | `CheatManager.TimeStepSeconds` | `0x40202` | `0x2549cd` | non-empty |
| `ToggleDebugCamera` | `CheatManager.ToggleDebugCamera` | `0x2080202` | `0x2570a5` | non-empty |
| `ViewActor <actorName:name>` | `CheatManager.ViewActor` | `0x40202` | `0x256a15` | non-empty |
| `ViewBot` | `CheatManager.ViewBot` | `0x40202` | `0x256c2e` | non-empty |
| `ViewClass <aClass:Class>` | `CheatManager.ViewClass` | `0x40202` | `0x256e3b` | non-empty |
| `ViewFlag` | `CheatManager.ViewFlag` | `0x40202` | `0x256aeb` | non-empty |
| `ViewPlayer <S:string>` | `CheatManager.ViewPlayer` | `0x40202` | `0x2568a0` | non-empty |
| `ViewSelf [bQuiet:bool]` | `CheatManager.ViewSelf` | `0x40202` | `0x25676d` | non-empty |
| `WriteToLog <Param:string>` | `CheatManager.WriteToLog` | `0x40202` | `0x254c0e` | non-empty |
| `SwitchToBestWeapon [bForceNewWeapon:bool]` | `Controller.SwitchToBestWeapon` | `0x2080202` | `0x1eeb7f` | non-empty |
| `MoreSpeed` | `DebugCameraController.MoreSpeed` | `0x40202` | `0x26deff` | non-empty |
| `NormalSpeed` | `DebugCameraController.NormalSpeed` | `0x40202` | `0x26df46` | non-empty |
| `SetFreezeRendering` | `DebugCameraController.SetFreezeRendering` | `0x40202` | `0x26de95` | non-empty |
| `DrawParticleSystemsInfo` | `DebugManager.DrawParticleSystemsInfo` | `0x8020202` | `0x27b1a8` | non-empty |
| `DumpAsyncTasks` | `DebugManager.DumpAsyncTasks` | `0x8020202` | `0x27bcda` | non-empty |
| `DumpReservations` | `DebugManager.DumpReservations` | `0x8020202` | `0x27bc3a` | non-empty |
| `DumpSessionState` | `DebugManager.DumpSessionState` | `0x8020202` | `0x27bb0e` | non-empty |
| `DumpVoiceRegistration` | `DebugManager.DumpVoiceRegistration` | `0x8020202` | `0x27bba4` | non-empty |
| `FlushDebug` | `DebugManager.FlushDebug` | `0x8020202` | `0x27a2cf` | non-empty |
| `KillPawn <PawnName:name>` | `DebugManager.KillPawn` | `0x8040202` | `0x27be61` | non-empty |
| `MultiFrameProfileScript <Cmd:string>` | `DebugManager.MultiFrameProfileScript` | `0x8040202` | `0x27bd6c` | non-empty |
| `SetEmitterPoolSize <Size:int>` | `DebugManager.SetEmitterPoolSize` | `0x8020202` | `0x27ba74` | non-empty |
| `SetPacketSimulationSettings <SimulationLevel:int>` | `DebugManager.SetPacketSimulationSettings` | `0x8020202` | `0x27bf54` | non-empty |
| `ShowActiveParticleSystems` | `DebugManager.ShowActiveParticleSystems` | `0x8020202` | `0x27b6ad` | non-empty |
| `ShowAnimDebug [StartingPoint:name]` | `DebugManager.ShowAnimDebug` | `0x8040202` | `0x27ad35` | non-empty |
| `ShowLightEnvComponent` | `DebugManager.ShowLightEnvComponent` | `0x8020202` | `0x27b9ff` | non-empty |
| `ShowNamedPawnAnimDebug <PawnName:name> [DebugStartPoint:name]` | `DebugManager.ShowNamedPawnAnimDebug` | `0x8020202` | `0x27b0d0` | non-empty |
| `ShowPawnAnimDebug <aClass:Class> [DebugStartPoint:name]` | `DebugManager.ShowPawnAnimDebug` | `0x8040202` | `0x27af0a` | non-empty |
| `ShowRenderedParticleSystems` | `DebugManager.ShowRenderedParticleSystems` | `0x8020202` | `0x27b6fe` | non-empty |
| `SPSS <SimulationLevel:int>` | `DebugManager.SPSS` | `0x8020202` | `0x27c170` | non-empty |
| `KillBots` | `GameInfo.KillBots` | `0x20200` | `0x2b9144` | empty |
| `DebugCreatePlayer <ControllerId:int>` | `GameViewportClient.DebugCreatePlayer` | `0x40202` | `0x2c17c3` | non-empty |
| `DebugRemovePlayer <ControllerId:int>` | `GameViewportClient.DebugRemovePlayer` | `0x40202` | `0x2c19ae` | non-empty |
| `SetConsoleTarget <PlayerIndex:int>` | `GameViewportClient.SetConsoleTarget` | `0x8040202` | `0x2c1aff` | non-empty |
| `SetSplit <Mode:int>` | `GameViewportClient.SetSplit` | `0x40202` | `0x2c1a43` | non-empty |
| `ShowTitleSafeArea` | `GameViewportClient.ShowTitleSafeArea` | `0x40202` | `0x2c1a8a` | non-empty |
| `SSSwapControllers` | `GameViewportClient.SSSwapControllers` | `0x40202` | `0x2c186a` | non-empty |
| `FXPlay <aClass:Class> <FXAnimPath:string>` | `HUD.FXPlay` | `0x40202` | `0x26f85e` | non-empty |
| `FXStop <aClass:Class>` | `HUD.FXStop` | `0x40202` | `0x26fb0f` | non-empty |
| `SetShowScores <bNewValue:bool>` | `HUD.SetShowScores` | `0x2020202` | `0x26f387` | non-empty |
| `ShowDebug [DebugType:name]` | `HUD.ShowDebug` | `0x2080202` | `0x26f476` | non-empty |
| `ShowHUD` | `HUD.ShowHUD` | `0x40202` | `0x26f1e2` | non-empty |
| `ShowScores` | `HUD.ShowScores` | `0x2080202` | `0x26f30f` | non-empty |
| `ToggleHUD` | `HUD.ToggleHUD` | `0x2020202` | `0x26f196` | non-empty |
| `SetBind <BindName:name> <Command:string>` | `Input.SetBind` | `0x840202` | `0x27389e` | non-empty |
| `DumpWeaponStats` | `InventoryManager.DumpWeaponStats` | `0x40302` | `0x2ec325` | non-empty |
| `Camera <NewMode:name>` | `PlayerController.Camera` | `0x40202` | `0x1fd10e` | non-empty |
| `CauseEvent <EventName:name>` | `PlayerController.CauseEvent` | `0x40202` | `0x210f7f` | non-empty |
| `CE <EventName:name>` | `PlayerController.CE` | `0x40202` | `0x210ff0` | non-empty |
| `ChangeTeam [TeamName:string]` | `PlayerController.ChangeTeam` | `0x40202` | `0x20448e` | non-empty |
| `ClearProgressMessages` | `PlayerController.ClearProgressMessages` | `0x40202` | `0x204743` | non-empty |
| `ConsoleKey <Key:name>` | `PlayerController.ConsoleKey` | `0x40202` | `0x21636f` | non-empty |
| `Jump` | `PlayerController.Dead.Jump` | `0x40202` | `0x20f9f9` | non-empty |
| `NextWeapon` | `PlayerController.Dead.NextWeapon` | `0x4020200` | `0x20f782` | empty |
| `PrevWeapon` | `PlayerController.Dead.PrevWeapon` | `0x4020200` | `0x20f7bc` | empty |
| `StartFire [FireModeNum:Byte]` | `PlayerController.Dead.StartFire` | `0x4020202` | `0x20f932` | non-empty |
| `ThrowWeapon` | `PlayerController.Dead.ThrowWeapon` | `0x4020200` | `0x20f7f6` | empty |
| `Use` | `PlayerController.Dead.Use` | `0x4020202` | `0x20f9b3` | non-empty |
| `DebugPause` | `PlayerController.DebugPause` | `0x40202` | `0x202baa` | non-empty |
| `FlushDebugForAllPlayers` | `PlayerController.FlushDebugForAllPlayers` | `0x40202` | `0x218bc6` | non-empty |
| `Fov <F:float>` | `PlayerController.Fov` | `0x40202` | `0x1fc965` | non-empty |
| `ListCE` | `PlayerController.ListCE` | `0x40202` | `0x21128a` | non-empty |
| `ListConsoleEvents` | `PlayerController.ListConsoleEvents` | `0x40202` | `0x211121` | non-empty |
| `LocalTravel <URL:string>` | `PlayerController.LocalTravel` | `0x40202` | `0x2027bf` | non-empty |
| `Mutate <MutateString:string>` | `PlayerController.Mutate` | `0x40202` | `0x1fca59` | non-empty |
| `NextWeapon` | `PlayerController.NextWeapon` | `0x2020202` | `0x203010` | non-empty |
| `Pause` | `PlayerController.Pause` | `0x40202` | `0x202c7c` | non-empty |
| `Jump` | `PlayerController.PlayerWaiting.Jump` | `0x40200` | `0x20d832` | empty |
| `NextWeapon` | `PlayerController.PlayerWaiting.NextWeapon` | `0x4020200` | `0x20d757` | empty |
| `PrevWeapon` | `PlayerController.PlayerWaiting.PrevWeapon` | `0x4020200` | `0x20d791` | empty |
| `StartFire [FireModeNum:Byte]` | `PlayerController.PlayerWaiting.StartFire` | `0x4020202` | `0x20da5a` | non-empty |
| `Suicide` | `PlayerController.PlayerWaiting.Suicide` | `0x4020200` | `0x20d86c` | empty |
| `SwitchToBestWeapon [bForceNewWeapon:bool]` | `PlayerController.PlayerWaiting.SwitchToBestWeapon` | `0x4080200` | `0x20d7f7` | non-empty |
| `PrevWeapon` | `PlayerController.PrevWeapon` | `0x2020202` | `0x202f6f` | non-empty |
| `QuickLoad` | `PlayerController.QuickLoad` | `0x40202` | `0x2028dc` | non-empty |
| `QuickSave` | `PlayerController.QuickSave` | `0x40202` | `0x202825` | non-empty |
| `RestartLevel` | `PlayerController.RestartLevel` | `0x2080202` | `0x202728` | non-empty |
| `StartFire [FireModeNum:Byte]` | `PlayerController.RoundEnded.StartFire` | `0x4020202` | `0x20ec9d` | non-empty |
| `Suicide` | `PlayerController.RoundEnded.Suicide` | `0x4020200` | `0x20e9d5` | empty |
| `ThrowWeapon` | `PlayerController.RoundEnded.ThrowWeapon` | `0x4020200` | `0x20eab7` | empty |
| `Use` | `PlayerController.RoundEnded.Use` | `0x4020200` | `0x20eaf1` | empty |
| `SaveActorConfig <actorName:name>` | `PlayerController.SaveActorConfig` | `0x40202` | `0x212743` | non-empty |
| `SaveClassConfig <ClassName:string>` | `PlayerController.SaveClassConfig` | `0x40202` | `0x212606` | non-empty |
| `Say <msg:string>` | `PlayerController.Say` | `0x40202` | `0x1fcd6e` | non-empty |
| `SendToConsole <Command:string>` | `PlayerController.SendToConsole` | `0x40202` | `0x216419` | non-empty |
| `SetName <S:string>` | `PlayerController.SetName` | `0x20202` | `0x2041b8` | non-empty |
| `SetProgressMessage <MessageType:Byte> <Message:string> [Title:string]` | `PlayerController.SetProgressMessage` | `0x80a02` | `0x20489e` | non-empty |
| `SetProgressTime <T:float>` | `PlayerController.SetProgressTime` | `0x40a02` | `0x204bed` | non-empty |
| `ShowMenu` | `PlayerController.ShowMenu` | `0x2020202` | `0x202d22` | non-empty |
| `RestartLevel` | `PlayerController.Spectating.RestartLevel` | `0x4080200` | `0x20cf21` | empty |
| `StartAltFire [FireModeNum:Byte]` | `PlayerController.Spectating.StartAltFire` | `0x4080202` | `0x20d1e0` | non-empty |
| `StartFire [FireModeNum:Byte]` | `PlayerController.Spectating.StartFire` | `0x4020202` | `0x20d16f` | non-empty |
| `Suicide` | `PlayerController.Spectating.Suicide` | `0x4020200` | `0x20cfc7` | empty |
| `ThrowWeapon` | `PlayerController.Spectating.ThrowWeapon` | `0x4020200` | `0x20d001` | empty |
| `Speech <Type:name> <Index:int> <Callsign:string>` | `PlayerController.Speech` | `0x40202` | `0x202619` | non-empty |
| `StartAltFire [FireModeNum:Byte]` | `PlayerController.StartAltFire` | `0x2080202` | `0x203230` | non-empty |
| `StartFire [FireModeNum:Byte]` | `PlayerController.StartFire` | `0x2020202` | `0x2030e1` | non-empty |
| `StopAltFire [FireModeNum:Byte]` | `PlayerController.StopAltFire` | `0x2080202` | `0x2032a7` | non-empty |
| `StopFire [FireModeNum:Byte]` | `PlayerController.StopFire` | `0x2020202` | `0x2031a2` | non-empty |
| `Suicide` | `PlayerController.Suicide` | `0x2020202` | `0x204092` | non-empty |
| `SwitchLevel <URL:string>` | `PlayerController.SwitchLevel` | `0x40202` | `0x2046bb` | non-empty |
| `SwitchTeam` | `PlayerController.SwitchTeam` | `0x20202` | `0x2043b0` | non-empty |
| `TeamSay <msg:string>` | `PlayerController.TeamSay` | `0x40202` | `0x1fcf80` | non-empty |
| `ThrowWeapon` | `PlayerController.ThrowWeapon` | `0x2020202` | `0x202ea7` | non-empty |
| `ToggleClientAdjustDebug` | `PlayerController.ToggleClientAdjustDebug` | `0x20202` | `0x210ab3` | non-empty |
| `Use` | `PlayerController.Use` | `0x2020202` | `0x20371c` | non-empty |
| `StartFire [FireModeNum:Byte]` | `PlayerController.WaitingForPawn.StartFire` | `0x4020202` | `0x20dd48` | non-empty |
| `ClearSmoothing` | `PlayerInput.ClearSmoothing` | `0x40202` | `0x2757b4` | non-empty |
| `InvertMouse` | `PlayerInput.InvertMouse` | `0x40202` | `0x274690` | non-empty |
| `InvertTurn` | `PlayerInput.InvertTurn` | `0x40202` | `0x274713` | non-empty |
| `Jump` | `PlayerInput.Jump` | `0x40202` | `0x2756c3` | non-empty |
| `SetSensitivity <F:float>` | `PlayerInput.SetSensitivity` | `0x40202` | `0x274796` | non-empty |
| `SmartJump` | `PlayerInput.SmartJump` | `0x40202` | `0x275748` | non-empty |

### HM_Engine.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `ScreenshotLightMode` | `HmCheatManager.ScreenshotLightMode` | `0x20202` | `0xf9353` | non-empty |
| `tdc` | `HmCheatManager.tdc` | `0x40202` | `0xf8e4b` | non-empty |
| `TeleportToCamera` | `HmCheatManager.TeleportToCamera` | `0x820202` | `0xf9427` | non-empty |
| `ToggleAIGodMode <aiName:string>` | `HmCheatManager.ToggleAIGodMode` | `0x20202` | `0xf9580` | non-empty |
| `ToggleDebugCamera` | `HmCheatManager.ToggleDebugCamera` | `0x4080202` | `0xf8e8f` | non-empty |
| `ToggleDebugQuicktimeShotCameraStrategy` | `HmCheatManager.ToggleDebugQuicktimeShotCameraStrategy` | `0x20202` | `0xf92ad` | non-empty |
| `ToggleDebugScreenshotCameraStrategy` | `HmCheatManager.ToggleDebugScreenshotCameraStrategy` | `0x20202` | `0xf9300` | non-empty |
| `UnlimitedAmmo` | `HmCheatManager.UnlimitedAmmo` | `0x40202` | `0xf9693` | non-empty |
| `AID` | `HmDebugManager.AID` | `0x8020202` | `0x105abc` | non-empty |
| `ErrorScreenDisable` | `HmDebugManager.ErrorScreenDisable` | `0x8020600` | `0x1059fb` | empty |
| `ErrorScreenHide` | `HmDebugManager.ErrorScreenHide` | `0x8020600` | `0x1059c2` | empty |
| `ErrorScreenNextPage` | `HmDebugManager.ErrorScreenNextPage` | `0x8020600` | `0x105950` | empty |
| `ErrorScreenPreviousPage` | `HmDebugManager.ErrorScreenPreviousPage` | `0x8020600` | `0x105989` | empty |
| `ESD` | `HmDebugManager.ESD` | `0x8020202` | `0x105a78` | non-empty |
| `ESH` | `HmDebugManager.ESH` | `0x8020202` | `0x105a34` | non-empty |
| `List [ActorClass:Class]` | `HmDebugManager.List` | `0x8020202` | `0x10616d` | non-empty |
| `LogCurrentStorageDevice` | `HmDebugManager.LogCurrentStorageDevice` | `0x8040202` | `0x1062ac` | non-empty |
| `PseudoPause` | `HmDebugManager.PseudoPause` | `0x8040202` | `0x105c00` | non-empty |
| `SetDebugTargetFromName <TargetName:name>` | `HmDebugManager.SetDebugTargetFromName` | `0x8020202` | `0x105dcc` | non-empty |
| `ShowDebugTargetAnimDebug` | `HmDebugManager.ShowDebugTargetAnimDebug` | `0x8040202` | `0x105cf8` | non-empty |
| `ShowWeapAnimDebug [PawnName:name] [DebugStartPoint:name]` | `HmDebugManager.ShowWeapAnimDebug` | `0x8040202` | `0x105fe9` | non-empty |
| `tdcs` | `HmDebugManager.tdcs` | `0x8040202` | `0x10641d` | non-empty |
| `ToggleDebugCameraStrategy` | `HmDebugManager.ToggleDebugCameraStrategy` | `0xa020202` | `0x106491` | non-empty |
| `DebugCamMoveForward <Speed:float>` | `HmPlayerController.DebugCamMoveForward` | `0x20202` | `0x15eedb` | non-empty |
| `DebugCamMoveUp <Speed:float>` | `HmPlayerController.DebugCamMoveUp` | `0x20202` | `0x15efd3` | non-empty |
| `DebugCamPitch <Speed:int>` | `HmPlayerController.DebugCamPitch` | `0x20202` | `0x15f04f` | non-empty |
| `DebugCamStop` | `HmPlayerController.DebugCamStop` | `0x20202` | `0x15f11b` | non-empty |
| `DebugCamStrafe <Speed:float>` | `HmPlayerController.DebugCamStrafe` | `0x20202` | `0x15ef57` | non-empty |
| `DebugCamTurn <Speed:int>` | `HmPlayerController.DebugCamTurn` | `0x20202` | `0x15f0cb` | non-empty |
| `Give <InvClass:Class>` | `HmPlayerController.Give` | `0x20202` | `0x164600` | non-empty |
| `ReloadWeapon` | `HmPlayerController.ReloadWeapon` | `0x80202` | `0x160383` | non-empty |
| `ShowDeviceSelectionUI [ForceShowUI:bool]` | `HmPlayerController.ShowDeviceSelectionUI` | `0x20202` | `0x162987` | non-empty |
| `ShowPauseMenu` | `HmPlayerController.ShowPauseMenu` | `0x2020a00` | `0x15ee75` | empty |
| `ShowTargetingDebug` | `HmPlayerController.ShowTargetingDebug` | `0x2080202` | `0x161223` | non-empty |
| `ToggleTargetingDebug` | `HmPlayerController.ToggleTargetingDebug` | `0x2080202` | `0x1611df` | non-empty |
| `UpdateLocalCacheOfProfileSettings` | `HmPlayerController.UpdateLocalCacheOfProfileSettings` | `0x2020202` | `0x163454` | non-empty |
| `ToggleMuzzleLight` | `HmWeapon.ToggleMuzzleLight` | `0x40202` | `0x190370` | non-empty |
| `ToggleWeaponImpactEffects` | `HmWeapon.ToggleWeaponImpactEffects` | `0x40202` | `0x1902d4` | non-empty |
| `ToggleWeaponSounds` | `HmWeapon.ToggleWeaponSounds` | `0x40202` | `0x190238` | non-empty |

### MP_ORB_Debris_BASE_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x209eec` | non-empty |

### TransGame.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `Kill [Type:string]` | `TnCheatManager.Kill` | `0x40202` | `0x498ea4` | non-empty |
| `PseudoGodAi` | `TnCheatManager.PseudoGodAi` | `0x40202` | `0x498bee` | non-empty |
| `SkipToCheckpoint <CheckpointName:string>` | `TnCheatManager.SkipToCheckpoint` | `0x40202` | `0x498adf` | non-empty |
| `AcceptFriendInvites` | `TnDebugManager.AcceptFriendInvites` | `0x8840202` | `0x4c2cf0` | non-empty |
| `AddAbilityResource [Amount:float]` | `TnDebugManager.AddAbilityResource` | `0x8040202` | `0x4c48e6` | non-empty |
| `AddBuff <BuffClass:Class> [BuffLevel:int]` | `TnDebugManager.AddBuff` | `0x8040202` | `0x4c1899` | non-empty |
| `AddXp <Specialty:string> [Amount:float]` | `TnDebugManager.AddXp` | `0x8040202` | `0x4c1d76` | non-empty |
| `AICommands` | `TnDebugManager.AICommands` | `0x8040202` | `0x4c0664` | non-empty |
| `AutoShot [SecondsBetweenShots:int]` | `TnDebugManager.AutoShot` | `0x8040202` | `0x4c3947` | non-empty |
| `AutoShotEmpty [SecondsBetweenShots:int]` | `TnDebugManager.AutoShotEmpty` | `0x8040202` | `0x4c39c2` | non-empty |
| `Buffalog` | `TnDebugManager.Buffalog` | `0x8040202` | `0x4c47c4` | non-empty |
| `ClearPickups` | `TnDebugManager.ClearPickups` | `0x8040202` | `0x4c27b0` | non-empty |
| `ClearXp` | `TnDebugManager.ClearXp` | `0x8040202` | `0x4c1e0c` | non-empty |
| `ContinueActivePlanner` | `TnDebugManager.ContinueActivePlanner` | `0x8020202` | `0x4c00f0` | non-empty |
| `Crash` | `TnDebugManager.Crash` | `0x8020202` | `0x4bf7f3` | non-empty |
| `DebugAddNewSkills <Specialty:string> <NewLevel:int>` | `TnDebugManager.DebugAddNewSkills` | `0x8040202` | `0x4c58e7` | non-empty |
| `DebugAddPower` | `TnDebugManager.DebugAddPower` | `0x8040202` | `0x4c25d5` | non-empty |
| `DebugCompleteMission <MapId:int> [Difficulty:int]` | `TnDebugManager.DebugCompleteMission` | `0x8040202` | `0x4c5df5` | non-empty |
| `DebugDowned` | `TnDebugManager.DebugDowned` | `0x8040202` | `0x4c2917` | non-empty |
| `DebugEndSurvivalRound` | `TnDebugManager.DebugEndSurvivalRound` | `0x8040202` | `0x4c2349` | non-empty |
| `DebugGame <Command:string>` | `TnDebugManager.DebugGame` | `0x8040202` | `0x4c26c3` | non-empty |
| `DebugGodMode` | `TnDebugManager.DebugGodMode` | `0x8040202` | `0x4c2891` | non-empty |
| `DebugGotoSurvivalRound <RoundNumber:int>` | `TnDebugManager.DebugGotoSurvivalRound` | `0x8040202` | `0x4c243f` | non-empty |
| `DebugInstallGame` | `TnDebugManager.DebugInstallGame` | `0x8040202` | `0x4c4d84` | non-empty |
| `DebugLoadCheckpointFile <I:int>` | `TnDebugManager.DebugLoadCheckpointFile` | `0x8040202` | `0x4c4bef` | non-empty |
| `DebugLoadingMovie` | `TnDebugManager.DebugLoadingMovie` | `0x8840202` | `0x4c521f` | non-empty |
| `DebugNotifyConnectionError [Title:string] [Message:string]` | `TnDebugManager.DebugNotifyConnectionError` | `0x8040202` | `0x4c5371` | non-empty |
| `DebugSaveCheckpointFile <I:int>` | `TnDebugManager.DebugSaveCheckpointFile` | `0x8040202` | `0x4c4b5a` | non-empty |
| `DebugSelect` | `TnDebugManager.DebugSelect` | `0x8020202` | `0x4bfad3` | non-empty |
| `DebugSetDifficulty <I:int>` | `TnDebugManager.DebugSetDifficulty` | `0x8040202` | `0x4c49bb` | non-empty |
| `DebugSurvivalEnemies` | `TnDebugManager.DebugSurvivalEnemies` | `0x8040202` | `0x4c2514` | non-empty |
| `DebugSurvivalZones` | `TnDebugManager.DebugSurvivalZones` | `0x8040202` | `0x4c2282` | non-empty |
| `DebugUnlockChallenge <Challenge:Object> <CompletionLevel:int>` | `TnDebugManager.DebugUnlockChallenge` | `0x8040202` | `0x4c21d8` | non-empty |
| `DebugUnlockCharacter <ChassisID:string>` | `TnDebugManager.DebugUnlockCharacter` | `0x8040202` | `0x4c5ead` | non-empty |
| `DebugUnlockPrimeMode` | `TnDebugManager.DebugUnlockPrimeMode` | `0x8020202` | `0x4c676d` | non-empty |
| `DebugXpEvent <XpEvent:Class>` | `TnDebugManager.DebugXpEvent` | `0x8040202` | `0x4c1a67` | non-empty |
| `DenyFriendInvites` | `TnDebugManager.DenyFriendInvites` | `0x8840202` | `0x4c2ee2` | non-empty |
| `DestroySelected` | `TnDebugManager.DestroySelected` | `0x8040202` | `0x4c14c2` | non-empty |
| `FlushAID` | `TnDebugManager.FlushAID` | `0x8040202` | `0x4c058e` | non-empty |
| `GAIDAR [InGoalToDebug:string]` | `TnDebugManager.GAIDAR` | `0x8020202` | `0x4c03bf` | non-empty |
| `GetGlobalCounterValue <CounterId:int>` | `TnDebugManager.GetGlobalCounterValue` | `0x8020202` | `0x4c4157` | non-empty |
| `IncrementGlobalCounter <CounterId:int>` | `TnDebugManager.IncrementGlobalCounter` | `0x8020202` | `0x4c403e` | non-empty |
| `JGI` | `TnDebugManager.JGI` | `0x8040202` | `0x4c34ae` | non-empty |
| `JoinGameInvite` | `TnDebugManager.JoinGameInvite` | `0x8040202` | `0x4c3330` | non-empty |
| `Killstreak <Killstreak:Class>` | `TnDebugManager.Killstreak` | `0x8040202` | `0x4c1b1e` | non-empty |
| `KillstreakReward <KillCount:int>` | `TnDebugManager.KillstreakReward` | `0x8040202` | `0x4c1bba` | non-empty |
| `LogCheckpoints` | `TnDebugManager.LogCheckpoints` | `0x8040202` | `0x4c4a5b` | non-empty |
| `LogControllers` | `TnDebugManager.LogControllers` | `0x8040202` | `0x4c5545` | non-empty |
| `LogPRIS [ShowAI:bool]` | `TnDebugManager.LogPRIS` | `0x8040202` | `0x4c64ae` | non-empty |
| `LogStorageDevice` | `TnDebugManager.LogStorageDevice` | `0x8040202` | `0x4c5721` | non-empty |
| `LogTeams` | `TnDebugManager.LogTeams` | `0x8020202` | `0x4c4eba` | non-empty |
| `NextPlannerHistory` | `TnDebugManager.NextPlannerHistory` | `0x8020202` | `0x4c0244` | non-empty |
| `PreviousPlannerHistory` | `TnDebugManager.PreviousPlannerHistory` | `0x8020202` | `0x4c01dd` | non-empty |
| `PrintKeyArchiveValues` | `TnDebugManager.PrintKeyArchiveValues` | `0x8820202` | `0x4c4482` | non-empty |
| `ProfileTacticalEvaluation` | `TnDebugManager.ProfileTacticalEvaluation` | `0x8020202` | `0x4bfc6b` | non-empty |
| `ReloadCharacerUI` | `TnDebugManager.ReloadCharacerUI` | `0x8040202` | `0x4c66be` | non-empty |
| `RemoveBuff <BuffClass:Class>` | `TnDebugManager.RemoveBuff` | `0x8040202` | `0x4c199b` | non-empty |
| `ResetCustomizationData` | `TnDebugManager.ResetCustomizationData` | `0x8040202` | `0x4c1eb7` | non-empty |
| `ResetDevelopmentLeaderboard <LeaderboardId:int> [LocalUserNum:int]` | `TnDebugManager.ResetDevelopmentLeaderboard` | `0x8040202` | `0x4c5a83` | non-empty |
| `ResetDevelopmentLeaderboards [LocalUserNum:int]` | `TnDebugManager.ResetDevelopmentLeaderboards` | `0x8040202` | `0x4c5b62` | non-empty |
| `SendFriendInvite <FriendUsername:string>` | `TnDebugManager.SendFriendInvite` | `0x8040202` | `0x4c2b86` | non-empty |
| `SendGameInvite` | `TnDebugManager.SendGameInvite` | `0x8840202` | `0x4c30d2` | non-empty |
| `SetAiPlanningThreshold <Milliseconds:int>` | `TnDebugManager.SetAiPlanningThreshold` | `0x8020202` | `0x4c13dd` | non-empty |
| `SetShowStartScreen <Show:bool>` | `TnDebugManager.SetShowStartScreen` | `0x8040202` | `0x4c59aa` | non-empty |
| `SGI` | `TnDebugManager.SGI` | `0x8040202` | `0x4c3234` | non-empty |
| `ShowAiPlanningGraph` | `TnDebugManager.ShowAiPlanningGraph` | `0x8020202` | `0x4c1368` | non-empty |
| `ShowAnimDebugOnTarget [StartingNode:name]` | `TnDebugManager.ShowAnimDebugOnTarget` | `0x8040202` | `0x4c15ef` | non-empty |
| `ShowColoredString <Message:string>` | `TnDebugManager.ShowColoredString` | `0x8840202` | `0x4c62c4` | non-empty |
| `ShowTacticalEvalDebug` | `TnDebugManager.ShowTacticalEvalDebug` | `0x8040202` | `0x4c1f6e` | non-empty |
| `ShowTacticalUpdateInfo` | `TnDebugManager.ShowTacticalUpdateInfo` | `0x8040202` | `0x4c1f25` | non-empty |
| `ShowTutorial <Type:int>` | `TnDebugManager.ShowTutorial` | `0x8040202` | `0x4c4cdb` | non-empty |
| `ShowWeaponAnimDebug [StartingNode:name]` | `TnDebugManager.ShowWeaponAnimDebug` | `0x8040202` | `0x4c173e` | non-empty |
| `Spectate [PlayerName:string]` | `TnDebugManager.Spectate` | `0x8040202` | `0x4c6172` | non-empty |
| `StepActivePlanner` | `TnDebugManager.StepActivePlanner` | `0x8020202` | `0x4c0176` | non-empty |
| `TakeDamage <Amount:float>` | `TnDebugManager.TakeDamage` | `0x8040202` | `0x4c1c69` | non-empty |
| `TestAddPlayerMet <NewName:string>` | `TnDebugManager.TestAddPlayerMet` | `0x8040202` | `0x4c6022` | non-empty |
| `TestNamedPawnDowned <PawnName:name>` | `TnDebugManager.TestNamedPawnDowned` | `0x8040202` | `0x4c45f4` | non-empty |
| `TestNamedPawnRevive <PawnName:name>` | `TnDebugManager.TestNamedPawnRevive` | `0x8040202` | `0x4c46c2` | non-empty |
| `TII` | `TnDebugManager.TII` | `0x8040202` | `0x4c1523` | non-empty |
| `ToggleAiIcons` | `TnDebugManager.ToggleAiIcons` | `0x8020202` | `0x4c131f` | non-empty |
| `ToggleAIWeaponAimValidatorEnabled` | `TnDebugManager.ToggleAIWeaponAimValidatorEnabled` | `0x8020202` | `0x4bfd42` | non-empty |
| `ToggleBloom` | `TnDebugManager.ToggleBloom` | `0x8040202` | `0x4c35c8` | non-empty |
| `ToggleCoopSpawnDebug` | `TnDebugManager.ToggleCoopSpawnDebug` | `0x8040202` | `0x4c11fc` | non-empty |
| `ToggleDebugCameraStrategy` | `TnDebugManager.ToggleDebugCameraStrategy` | `0xc020202` | `0x4c29ce` | non-empty |
| `ToggleDecalDebug` | `TnDebugManager.ToggleDecalDebug` | `0x8040202` | `0x4c119e` | non-empty |
| `ToggleDepthOfField` | `TnDebugManager.ToggleDepthOfField` | `0x8040202` | `0x4c3611` | non-empty |
| `ToggleFuelDebug` | `TnDebugManager.ToggleFuelDebug` | `0x8040202` | `0x4c125a` | non-empty |
| `ToggleIgnoreInput` | `TnDebugManager.ToggleIgnoreInput` | `0x8040202` | `0x4c1449` | non-empty |
| `ToggleInvisibleToAI` | `TnDebugManager.ToggleInvisibleToAI` | `0x8040202` | `0x4c36ec` | non-empty |
| `ToggleMotionBlur` | `TnDebugManager.ToggleMotionBlur` | `0x8040202` | `0x4c365a` | non-empty |
| `TogglePawnTransparency` | `TnDebugManager.TogglePawnTransparency` | `0x8040202` | `0x4c5414` | non-empty |
| `ToggleSceneEffect` | `TnDebugManager.ToggleSceneEffect` | `0x8040202` | `0x4c36a3` | non-empty |
| `ToggleShowAIReachability` | `TnDebugManager.ToggleShowAIReachability` | `0x8040202` | `0x4c12b8` | non-empty |
| `ToggleStepPlannerEnabled` | `TnDebugManager.ToggleStepPlannerEnabled` | `0x8020202` | `0x4c0010` | non-empty |
| `ToggleTacticalPathNodeDebug` | `TnDebugManager.ToggleTacticalPathNodeDebug` | `0x8040202` | `0x4c1140` | non-empty |
| `LongAdjustDebug` | `TnHUD.LongAdjustDebug` | `0x40202` | `0x52c418` | non-empty |
| `SetShowScores <bNewValue:bool>` | `TnHUD.SetShowScores` | `0x4020202` | `0x52baa2` | non-empty |
| `ShowDebug [DebugType:name]` | `TnHUD.ShowDebug` | `0x4080202` | `0x52c04d` | non-empty |
| `ShowScores` | `TnHUD.ShowScores` | `0x4080202` | `0x52b99a` | non-empty |
| `ToggleHUD` | `TnHUD.ToggleHUD` | `0x4020202` | `0x52bbd3` | non-empty |
| `SetTossVel <X:float> <Y:float> <Z:float>` | `TnInventoryManager.SetTossVel` | `0x40202` | `0x544e6f` | non-empty |
| `DebugGiveAbility <AbilityUniqueId:string>` | `TnPawn.DebugGiveAbility` | `0x20202` | `0x3caf7b` | non-empty |
| `DebugGiveSkill <SkillUniqueId:string> <Level:int>` | `TnPawn.DebugGiveSkill` | `0x20202` | `0x3caea5` | non-empty |
| `HeatCheat` | `TnPawn.HeatCheat` | `0x40202` | `0x3cadd0` | non-empty |
| `TestDowned` | `TnPawn.TestDowned` | `0x20202` | `0x3e1dd0` | non-empty |
| `TestDownedDelayed` | `TnPawn.TestDownedDelayed` | `0x20202` | `0x3e1e10` | non-empty |
| `TestRevive` | `TnPawn.TestRevive` | `0x20202` | `0x3e1ea9` | non-empty |
| `NextWeapon` | `TnPlayerController.AnimControl.NextWeapon` | `0x4020200` | `0x5c4f9c` | empty |
| `StartFire [FireModeNum:Byte]` | `TnPlayerController.AnimControl.StartFire` | `0x4020200` | `0x5c4f61` | non-empty |
| `TriggerAbility <number:int>` | `TnPlayerController.AnimControl.TriggerAbility` | `0x4020200` | `0x5c5002` | empty |
| `UnTriggerAbility <number:int>` | `TnPlayerController.AnimControl.UnTriggerAbility` | `0x4020200` | `0x5c5068` | empty |
| `FineAim` | `TnPlayerController.ControllingTurret.FineAim` | `0x4020202` | `0x5c5c69` | non-empty |
| `NextWeapon` | `TnPlayerController.ControllingTurret.NextWeapon` | `0x4020200` | `0x5c57a1` | empty |
| `PressKeyboardInteractKey` | `TnPlayerController.ControllingTurret.PressKeyboardInteractKey` | `0x4020202` | `0x5c5e36` | non-empty |
| `PressXButton` | `TnPlayerController.ControllingTurret.PressXButton` | `0x4020202` | `0x5c5db8` | non-empty |
| `ReleaseKeyboardInteractKey` | `TnPlayerController.ControllingTurret.ReleaseKeyboardInteractKey` | `0x4020200` | `0x5c5e7a` | empty |
| `ReleaseXButton` | `TnPlayerController.ControllingTurret.ReleaseXButton` | `0x4020200` | `0x5c5dfc` | empty |
| `TriggerAbility <number:int>` | `TnPlayerController.ControllingTurret.TriggerAbility` | `0x4020200` | `0x5c5807` | empty |
| `UnTriggerAbility <number:int>` | `TnPlayerController.ControllingTurret.UnTriggerAbility` | `0x4020200` | `0x5c586d` | empty |
| `Dance` | `TnPlayerController.Dance` | `0x20202` | `0x5bb690` | non-empty |
| `DownedSuicide` | `TnPlayerController.Dead.DownedSuicide` | `0x4020200` | `0x5ce1e0` | empty |
| `FineAim` | `TnPlayerController.Dead.FineAim` | `0x4020200` | `0x5cdfe4` | empty |
| `NextWeapon` | `TnPlayerController.Dead.NextWeapon` | `0x4020200` | `0x5ce254` | empty |
| `PressKeyboardInteractKey` | `TnPlayerController.Dead.PressKeyboardInteractKey` | `0x4020200` | `0x5ce092` | empty |
| `PressXButton` | `TnPlayerController.Dead.PressXButton` | `0x4020200` | `0x5ce058` | empty |
| `StartAscend` | `TnPlayerController.Dead.StartAscend` | `0x4020200` | `0x5ce16c` | empty |
| `StartBoost` | `TnPlayerController.Dead.StartBoost` | `0x4020200` | `0x5ce01e` | empty |
| `StartDescend` | `TnPlayerController.Dead.StartDescend` | `0x4020200` | `0x5ce1a6` | empty |
| `StartFocus` | `TnPlayerController.Dead.StartFocus` | `0x4020200` | `0x5cdfaa` | empty |
| `StartVehicleSpecialMove` | `TnPlayerController.Dead.StartVehicleSpecialMove` | `0x4020200` | `0x5ce0cc` | empty |
| `TossGrenade` | `TnPlayerController.Dead.TossGrenade` | `0x4020200` | `0x5ce21a` | empty |
| `TriggerAbility <number:int>` | `TnPlayerController.Dead.TriggerAbility` | `0x4020200` | `0x5ce132` | empty |
| `TriggerKillstreak` | `TnPlayerController.Dead.TriggerKillstreak` | `0x4020200` | `0x5ce28e` | empty |
| `Use` | `TnPlayerController.Dead.Use` | `0x4020200` | `0x5ce604` | empty |
| `DebugUnlockAchievement <AchievementId:int>` | `TnPlayerController.DebugUnlockAchievement` | `0x8040202` | `0x5be8d5` | non-empty |
| `DebugUnlockAllAchievements` | `TnPlayerController.DebugUnlockAllAchievements` | `0x8040202` | `0x5be91e` | non-empty |
| `DownedSuicide` | `TnPlayerController.DownedSuicide` | `0x2020202` | `0x5bf69a` | non-empty |
| `FakeScoreKill [Kills:int] [DamageType:Class]` | `TnPlayerController.FakeScoreKill` | `0x40202` | `0x5d1d57` | non-empty |
| `FakeScoreObjective [num:int]` | `TnPlayerController.FakeScoreObjective` | `0x40202` | `0x5d1f15` | non-empty |
| `FAut` | `TnPlayerController.FAut` | `0x20202` | `0x5d292d` | non-empty |
| `FDec` | `TnPlayerController.FDec` | `0x20202` | `0x5d28b2` | non-empty |
| `FineAim` | `TnPlayerController.FineAim` | `0x2020200` | `0x5c11a8` | empty |
| `SetDeferredFire [FireModeNum:Byte]` | `TnPlayerController.Grabbed.SetDeferredFire` | `0x4020200` | `0x5c6003` | non-empty |
| `SetDeferredFire [FireModeNum:Byte]` | `TnPlayerController.GuidingMissile.SetDeferredFire` | `0x4020200` | `0x5c34b5` | non-empty |
| `TriggerAbility <number:int>` | `TnPlayerController.GuidingMissile.TriggerAbility` | `0x4020202` | `0x5c3ade` | non-empty |
| `Interact` | `TnPlayerController.Interact` | `0x20202` | `0x5bf0af` | non-empty |
| `InteractCommand0` | `TnPlayerController.InteractCommand0` | `0x20202` | `0x5bf10c` | non-empty |
| `InteractCommand1` | `TnPlayerController.InteractCommand1` | `0x20202` | `0x5bf14e` | non-empty |
| `LogXpData` | `TnPlayerController.LogXpData` | `0x20202` | `0x5cfe82` | non-empty |
| `Melee` | `TnPlayerController.Melee` | `0x2020200` | `0x5c1bb2` | empty |
| `NextWeapon` | `TnPlayerController.NextWeapon` | `0x4020202` | `0x5c00ac` | non-empty |
| `PressJump` | `TnPlayerController.PlaneDowned.PressJump` | `0x4020200` | `0x5c8c8f` | empty |
| `StartVehicleSpecialMove` | `TnPlayerController.PlaneDowned.StartVehicleSpecialMove` | `0x4020200` | `0x5c8d6d` | empty |
| `PressJump` | `TnPlayerController.PlayerInCarForm.PressJump` | `0x4020202` | `0x5c93c7` | non-empty |
| `StartVehicleSpecialMove` | `TnPlayerController.PlayerInCarForm.StartVehicleSpecialMove` | `0x4020202` | `0x5c932b` | non-empty |
| `StopVehicleSpecialMove` | `TnPlayerController.PlayerInCarForm.StopVehicleSpecialMove` | `0x4020202` | `0x5c9379` | non-empty |
| `StartAscend` | `TnPlayerController.PlayerInPlaneForm.StartAscend` | `0x4020202` | `0x5c7cbf` | non-empty |
| `StartDescend` | `TnPlayerController.PlayerInPlaneForm.StartDescend` | `0x4020202` | `0x5c7d5b` | non-empty |
| `StartVehicleSpecialMove` | `TnPlayerController.PlayerInPlaneForm.StartVehicleSpecialMove` | `0x4020202` | `0x5c7ba5` | non-empty |
| `StopAscend` | `TnPlayerController.PlayerInPlaneForm.StopAscend` | `0x4020202` | `0x5c7d0d` | non-empty |
| `StopDescend` | `TnPlayerController.PlayerInPlaneForm.StopDescend` | `0x4020202` | `0x5c7da9` | non-empty |
| `StopVehicleSpecialMove` | `TnPlayerController.PlayerInPlaneForm.StopVehicleSpecialMove` | `0x4020202` | `0x5c7bf3` | non-empty |
| `PressJump` | `TnPlayerController.PlayerInTankForm.PressJump` | `0x4020202` | `0x5c7575` | non-empty |
| `StartVehicleSpecialMove` | `TnPlayerController.PlayerInTankForm.StartVehicleSpecialMove` | `0x4020202` | `0x5c7641` | non-empty |
| `TriggerAbility <number:int>` | `TnPlayerController.PlayerInVehicleForm.TriggerAbility` | `0x4020200` | `0x5c69c3` | empty |
| `UnTriggerAbility <number:int>` | `TnPlayerController.PlayerInVehicleForm.UnTriggerAbility` | `0x4020200` | `0x5c6a29` | empty |
| `Use` | `TnPlayerController.PlayerInVehicleForm.Use` | `0x4020200` | `0x5c695d` | empty |
| `ToggleSpectator` | `TnPlayerController.PlayerSpectating.ToggleSpectator` | `0x4080202` | `0x5ce8ae` | non-empty |
| `FineAim` | `TnPlayerController.PlayerWalking.FineAim` | `0x4020202` | `0x5c2b3f` | non-empty |
| `Melee` | `TnPlayerController.PlayerWalking.Melee` | `0x4020202` | `0x5c2614` | non-empty |
| `PressJump` | `TnPlayerController.PlayerWalking.PressJump` | `0x4020202` | `0x5c23d4` | non-empty |
| `TossGrenade` | `TnPlayerController.PlayerWalking.TossGrenade` | `0x4020202` | `0x5c29f4` | non-empty |
| `TriggerAbility <number:int>` | `TnPlayerController.PlayerWalking.TriggerAbility` | `0x4020202` | `0x5c2d9c` | non-empty |
| `PressBlock` | `TnPlayerController.PressBlock` | `0x2020202` | `0x5c1970` | non-empty |
| `PressJump` | `TnPlayerController.PressJump` | `0x2020200` | `0x5bfca6` | empty |
| `PressKeyboardInteractKey` | `TnPlayerController.PressKeyboardInteractKey` | `0x2020202` | `0x5bf5e4` | non-empty |
| `PressTransform` | `TnPlayerController.PressTransform` | `0x20202` | `0x5c17f7` | non-empty |
| `PressXButton` | `TnPlayerController.PressXButton` | `0x2020202` | `0x5bf52e` | non-empty |
| `QuitGame <QuitGameMethod:Byte>` | `TnPlayerController.QuitGame` | `0x20202` | `0x5cab2c` | non-empty |
| `RecordClientAdjust` | `TnPlayerController.RecordClientAdjust` | `0x40202` | `0x5d205e` | non-empty |
| `ReleaseBlock` | `TnPlayerController.ReleaseBlock` | `0x2020202` | `0x5c1a00` | non-empty |
| `ReleaseDownedSuicide` | `TnPlayerController.ReleaseDownedSuicide` | `0x2020202` | `0x5bf70b` | non-empty |
| `ReleaseKeyboardInteractKey` | `TnPlayerController.ReleaseKeyboardInteractKey` | `0x2020202` | `0x5bf64d` | non-empty |
| `ReleaseXButton` | `TnPlayerController.ReleaseXButton` | `0x2020202` | `0x5bf597` | non-empty |
| `RunKeyboardUi` | `TnPlayerController.RunKeyboardUi` | `0x20202` | `0x5cef25` | non-empty |
| `SelectCharacter <CharacterName:string> <PresetCharacterType:Byte>` | `TnPlayerController.SelectCharacter` | `0x820202` | `0x5cfcd9` | non-empty |
| `SetDeferredFire [FireModeNum:Byte]` | `TnPlayerController.SetDeferredFire` | `0x2020202` | `0x5be35e` | non-empty |
| `ShowMenu` | `TnPlayerController.ShowMenu` | `0x4020202` | `0x5cd994` | non-empty |
| `SkipMatinee` | `TnPlayerController.SkipMatinee` | `0x20202` | `0x5d20f8` | non-empty |
| `StartAscend` | `TnPlayerController.StartAscend` | `0x2020200` | `0x5c670c` | empty |
| `StartBoost` | `TnPlayerController.StartBoost` | `0x2020202` | `0x5c64dd` | non-empty |
| `StartDescend` | `TnPlayerController.StartDescend` | `0x2020200` | `0x5c6780` | empty |
| `StartFocus` | `TnPlayerController.StartFocus` | `0x2020202` | `0x5bfce0` | non-empty |
| `StartVehicleSpecialMove` | `TnPlayerController.StartVehicleSpecialMove` | `0x2020200` | `0x5c662e` | empty |
| `StopAscend` | `TnPlayerController.StopAscend` | `0x2020200` | `0x5c6746` | empty |
| `StopBoost` | `TnPlayerController.StopBoost` | `0x2020202` | `0x5c6534` | non-empty |
| `StopDeferredFire [FireModeNum:Byte]` | `TnPlayerController.StopDeferredFire` | `0x20202` | `0x5be3dc` | non-empty |
| `StopDescend` | `TnPlayerController.StopDescend` | `0x2020200` | `0x5c67ba` | empty |
| `StopFineAim` | `TnPlayerController.StopFineAim` | `0x20202` | `0x5c11e2` | non-empty |
| `StopFocus` | `TnPlayerController.StopFocus` | `0x20202` | `0x5bfd2d` | non-empty |
| `StopVehicleSpecialMove` | `TnPlayerController.StopVehicleSpecialMove` | `0x2020200` | `0x5c6668` | empty |
| `StressTest` | `TnPlayerController.StressTest` | `0x20202` | `0x5d4b75` | non-empty |
| `SwitchSpectatorTarget <Direction:int>` | `TnPlayerController.SwitchSpectatorTarget` | `0x2020202` | `0x5c3df5` | non-empty |
| `ToggleFineAim` | `TnPlayerController.ToggleFineAim` | `0x20202` | `0x5c1230` | non-empty |
| `ToggleRumble` | `TnPlayerController.ToggleRumble` | `0x40202` | `0x5be1ec` | non-empty |
| `ToggleSpectator` | `TnPlayerController.ToggleSpectator` | `0x2080202` | `0x5ce6ba` | non-empty |
| `ToggleTargetingDebug` | `TnPlayerController.ToggleTargetingDebug` | `0x4080202` | `0x5bdfc9` | non-empty |
| `TossGrenade` | `TnPlayerController.TossGrenade` | `0x2020200` | `0x5c00f5` | empty |
| `TransformPP` | `TnPlayerController.TransformPP` | `0x20202` | `0x5cefee` | non-empty |
| `TriggerAbility <number:int>` | `TnPlayerController.TriggerAbility` | `0x2020200` | `0x5be98e` | empty |
| `TriggerKillstreak` | `TnPlayerController.TriggerKillstreak` | `0x2020202` | `0x5becf0` | non-empty |
| `TryRevive` | `TnPlayerController.TryRevive` | `0x20202` | `0x5bf9b6` | non-empty |
| `UnTriggerAbility <number:int>` | `TnPlayerController.UnTriggerAbility` | `0x2020202` | `0x5be9f4` | non-empty |
| `UpdateLocalCacheOfProfileSettings` | `TnPlayerController.UpdateLocalCacheOfProfileSettings` | `0x4020202` | `0x5cc979` | non-empty |
| `WeaponMelee` | `TnPlayerController.WeaponMelee` | `0x20202` | `0x5c1b48` | non-empty |
| `PressKeyboardInteractKey` | `TnPlayerControllerSurvival.PressKeyboardInteractKey` | `0x4020202` | `0x5d7b61` | non-empty |
| `PressXButton` | `TnPlayerControllerSurvival.PressXButton` | `0x4020202` | `0x5d7abb` | non-empty |
| `ReleaseKeyboardInteractKey` | `TnPlayerControllerSurvival.ReleaseKeyboardInteractKey` | `0x4020202` | `0x5d7bb4` | non-empty |
| `ReleaseXButton` | `TnPlayerControllerSurvival.ReleaseXButton` | `0x4020202` | `0x5d7b0e` | non-empty |

### mods/A2_KON_BASE_m.xxx

| Command syntax | Owner/state | Function flags | Function offset | Body |
|---|---|---|---|---|
| `DebugFinishCampaign` | `TransContent.TnCampaignGame.DebugFinishCampaign` | `0x20202` | `0x1f1a58` | non-empty |

## Ghidra changes

Saved the Ghidra program after correcting the token parser prototype, creating three previously missing function definitions, assigning descriptive names, and adding comments/bookmarks. Existing non-default names and comments were preserved. No executable bytes were patched.

| Address | Name |
|---|---|
| `0x10c4ae50` | `ProcessViewportInputCommands` |
| `0x10c4c710` | `ProcessScaleformCommands` |
| `0x10e45fc0` | `ProcessDebugCommandFallback` |
| `0x1100dcd0` | `ProcessAllocationDumpCommand` |
| `0x114419d0` | `ProcessPathBuildingCommands` |
| `0x11441ae0` | `ProcessOctreeCommands` |
| `0x11458540` | `ProcessNavigationOctreeCommands` |
| `0x11476b80` | `ProcessDiagnosticCommands` |
| `0x11490c80` | `ProcessChartCommands` |
| `0x114e8a70` | `ProcessTextureStreamingCommand` |
| `0x114fb6e0` | `ProcessSoundCommands` |
| `0x11513ab0` | `ProcessScalabilityCommands` |
| `0x11549d60` | `ProcessLightEnvironmentCommand` |
| `0x11587090` | `ProcessCollisionDebugCommands` |
| `0x115c7040` | `ProcessViewportCommands` |
| `0x115cb680` | `ProcessEngineCommands` |
| `0x115fdcc0` | `ProcessPhysicsCommands` |
| `0x116037e0` | `ProcessInputBindingCommands` |
| `0x11629950` | `ProcessNetworkDebugCommands` |
| `0x116811c0` | `ProcessGammaCommand` |
| `0x1169d890` | `ProcessGameEngineCommands` |
| `0x116cc9b0` | `ProcessCoreCommands` |
| `0x116f6d00` | `ProcessObjectCommands` |
| `0x116fff60` | `ParseCommand` |
| `0x11708070` | `ProcessTaggedAllocationDumpCommand` |
| `0x1170d170` | `ProcessAllocatorCommands` |
| `0x1172b790` | `ProcessObjectStateCommand` |
| `0x117dc1b0` | `ProcessScriptDebuggerCommands` |
| `0x117fb270` | `ProcessWindowsClientCommands` |

## Reproduction and evidence

The accompanying JSON contains all raw parser call sites, table-derived native forms, 415 reflected definitions, argument flags, decompressed package offsets, function-flag offsets, per-package scan counts, and renamed function addresses. The readable command list groups distinct names while retaining every signature.

Package header and export-table handling was cross-checked against [UEViewer's Transformers-specific UE3 reader](https://github.com/gildor2/UEViewer/blob/master/Unreal/UnrealPackage/UnPackage3.cpp). Command findings themselves come from the installed executable and packages.


## Disclosure
This is entirely AI-generated.
