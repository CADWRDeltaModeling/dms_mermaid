``` mermaid
%% New flowchart for atmospheric simulation
flowchart TD

    %% Define processes -----------------------
    barotropic{Barotropic Simulation}
    baroclinic{Baroclinic Simulation}
    atminp(["Start: 
        atmospheric input"])
    thi(["Start: Time history input"])
    ocean_elev(["Start: ocean elevation input"])
    grid(["Start: gridding"])
    uv3d(["uv3d"])
    nudge(["Start: nudging input"])
    hotstart(["Start: hotstart input"])

    %% define code or simple process -----------------------
    atmos["Copy to​ /scratch/data/atmos/hrrr/ on HPC5"]
    mklinks["Run: make_links_full.py"]
    elev_download["Download: elevation files using download_noaa"]
    gen_elev2d["Run: gen_elev2d"]
    interpvar["interpolate_variables7"]
    thi_update["Update (Eli): BayDeltaSCHISM/data/time_history"]
    multi_clip["Run: multi_clip.py"]
    channel_depletion["Update (Eli): BayDeltaSCHISM/data/channel_depletion"]
    nudge_py["Run: nudging.py"]
    hot_py["Run: hotstart.py"]
    prep_sch["Run: prepare_schism"]
    cruise["Download: USGS Polaris cruise data"]

    %% define documents -----------------------
    mesh@{ shape: doc, label: "*.2dm"}
    hgrid@{ shape: doc, label: "hgrid.gr3"}
    vgrid2@{ shape: doc, label: "vgrid.in.2d"}
    vgrid3@{ shape: doc, label: "vgrid.in.3d"}
    elev2d@{ shape: doc, label: "elev2d.th.nc"}
    uv3d@{ shape: doc, label: "uv3d.th.nc"}
    hot_nc@{ shape: doc, label: "hotstart.nc"}
    raw_hotstart@{shape: doc, label: "hotstart_it_{timestep}.nc"}
    
    %% multi-file docs: 
    other_geo@{ shape: docs, label: "*.gr3 & hydraulics.in & fluxflag.prop"}
    thi_elapsed@{ shape: docs, label: "*.th in elapsed time"}
    hrrrin@{ shape: docs, label: "HRRR files"}
    hrrr@{ shape: docs, label: "HRRR files"}
    sflux@{ shape: docs, label: "sflux/*.nc files"}
    elev_ts@{ shape: docs, label: "Elevation time series in .csv"}
    dem@{ shape: docs, label: "DEM files"}
    gyaml@{ shape: docs, label: "*.yaml geographic configs"}
    nudge_ts@{ shape: docs, label: "Nudging time series in .csv"}
    nudge_nc@{ shape: docs, label: "{MOD}_nu.nc & {MOD_nudge.gr3}"}
    hot_data@{ shape: docs, label: "Hotstart data in .csv"}
    hotstart_combine_data@{shape: docs, label: "hotstart_*_{timestep}.nc"}
    hycom_input@{shape: docs, label: "hycom yaml input"}
    baroclinic_out@{shape: docs, label: "*.nc, *.staout, mirror.out"}
    trop_out@{ shape: docs, label: "*.nc output files"}

    %% styles
    style barotropic fill:#FFF9C4
    style baroclinic fill:#BBDEFB
    style geographic stroke:none
    style barotropic_group stroke:none
    style tropic2clinic stroke:none
    style elevation stroke:none
    style atmospheric stroke:none
    style nudging stroke:none
    style boundary_timeseries stroke:none
    style hotstarting stroke:none
    %% ========================FLOWCHART===============================
    
    %% geographic data -----------------------
    subgraph geographic
        grid --> mesh --> prep_sch
        dem & gyaml --> prep_sch
        prep_sch --> hgrid
        prep_sch --> vgrid2
        prep_sch --> vgrid3
        prep_sch --> other_geo
    end
    %% timeseries inputs -----------------------
    subgraph boundary_timeseries
        thi --> thi_update
        thi --> channel_depletion
        thi_update & channel_depletion --> multi_clip
        multi_clip --> thi_elapsed
    end
    %% atmospheric data -----------------------
    subgraph atmospheric
        atminp --> hrrrin --> atmos
        atmos --> hrrr --> mklinks
        mklinks --> sflux
        %% mklinks -- "*.yaml???" --> barotropic & baroclinic
    end
    %% boundary_timeseries ~~~ geographic ~~~ atmospheric
    %% bartropic run
    subgraph barotropic_group
        hgrid --> barotropic
        vgrid2 --> barotropic
        other_geo --> barotropic   
        elev2d --> barotropic 
        thi_elapsed -->  barotropic
        sflux --> barotropic
    end
    %% barotropic to baroclinic -----------------------
    subgraph tropic2clinic
        barotropic --> trop_out --> interpvar
        elev2d --> baroclinic
        hgrid --> baroclinic
        vgrid3 --> baroclinic
        other_geo --> baroclinic
        hgrid --> interpvar
        vgrid3 --> interpvar
        interpvar --> uv3d --> baroclinic
        thi_elapsed --> baroclinic
        sflux --> baroclinic
    end
    %% output -----------------------
    baroclinic --> baroclinic_out
    %% elev2d -----------------------
    subgraph elevation
        ocean_elev --> elev_download
        elev_download --> elev_ts
        elev_ts --> gen_elev2d
        hgrid --> gen_elev2d
        gen_elev2d --> elev2d
    end

    %% nudging process -----------------------
    subgraph nudging
        hgrid --> nudge_py
        vgrid3 --> nudge_py
        nudge --> nudge_ts --> nudge_py --> nudge_nc --> baroclinic
        %% hycom input -----------------------
        hycom_input --> nudge_py
    end

    %% hotstart process -----------------------
    subgraph hotstarting
        hotstart -- starting from 0:00? --> cruise --> hot_data --> hot_py --> hot_nc --> baroclinic
        hgrid & vgrid2 & vgrid3 --> hot_py
        %% hotstart combine -----------------------
        hotstart -- starting from previous run? --> hotstart_combine_data --> combine_hotstart7 --> raw_hotstart --> baroclinic
    end

```