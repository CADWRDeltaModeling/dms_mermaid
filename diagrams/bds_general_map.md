
``` mermaid
---
config:
  look: classic
  theme: default
---
flowchart TD
    
    %% Time Series Inputs -------------------------------------------------------------------------
    subgraph bd [Time Series Inputs]
        time_ins("Inflow Boundaries")
        ocean_in("Ocean Boundary")
        atmos_in("Atmospheric Forcings")
    end
    %% Stlye
    style bd fill:#c8bce0, stroke:#3e3254, color:#FFFFFF
    style time_ins fill:#e9e1f7, stroke:None
    style ocean_in fill:#e9e1f7, stroke:None
    style atmos_in fill:#e9e1f7, stroke:None
    click time_ins "https://cadwrdeltamodeling.github.io/BayDeltaSCHISM/topics/flow_boundary.html" _blank
    click ocean_in "https://cadwrdeltamodeling.github.io/BayDeltaSCHISM/topics/ocean.html" _blank
    click atmos_in "https://cadwrdeltamodeling.github.io/BayDeltaSCHISM/topics/atmospheric.html" _blank

    %% Spatial Inputs -------------------------------------------------------------------------
    subgraph si [Spatial Inputs]
        nudge("Nudging")
        hotstart("Hotstart")
        veg("Vegetation")
    end
    %% Stlye
    style si fill:#e3cbb1, stroke:#473a2c, color:#FFFFFF
    style nudge fill:#f2e7da, stroke:None
    style hotstart fill:#f2e7da, stroke:None
    style veg fill:#f2e7da, stroke:None
    click nudge "https://cadwrdeltamodeling.github.io/BayDeltaSCHISM/topics/nudging.html" _blank
    click hotstart "https://cadwrdeltamodeling.github.io/BayDeltaSCHISM/topics/hotstart.html" _blank
    click veg "https://cadwrdeltamodeling.github.io/BayDeltaSCHISM/topics/vegetation.html" _blank


    schism{SCHISM Simulation}
    style schism fill:#7fa6b3, stroke:None, color:#FFFFFF

    %% Gridding -------------------------------------------------------------------------
    subgraph mesh [Gridding]
    end
    style mesh fill:#8a5c6f, stroke:#1a0f13, color:#FFFFFF
    click mesh "https://cadwrdeltamodeling.github.io/BayDeltaSCHISM/topics/mesh.html" _blank

    %% Connections -------------------------------------------------------------------------
    mesh --> si
    bd --> schism
    si --> schism
```