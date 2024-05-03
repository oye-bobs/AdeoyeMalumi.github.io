# ESIIL API Project - The Cameron Peak Fire, Colorado

The Cameron Peak Fire was the largest fire in Colorado history, with 326
square miles burned.

## Observing vegetation health from space

We will look at the destruction and recovery of vegetation using NDVI
(Normalized Difference Vegetation Index). How does it work? First, we
need to learn about spectral reflectance signatures.

Every object reflects some wavelengths of light more or less than
others. We can see this with our eyes, since, for example, plants
reflect a lot of green in the summer, and then as that green diminishes
in the fall they look more yellow or orange. The image below shows
spectral signatures for water, soil, and vegetation:

![](https://seos-project.eu/remotesensing/images/Reflexionskurven.jpg)
\> Image source: [SEOS
Project](https://seos-project.eu/remotesensing/remotesensing-c01-p06.html)

Healthy vegetation reflects a lot of Near-InfraRed (NIR) radiation. Dead
ve Less healthy vegetation reflects a similar amounts of the visible
light spectra, but less NIR radiation. We don’t see a huge drop in Green
radiation until the plant is very stressed or dead. That means that NIR
allows us to get ahead of what we can see with our eyes.

![Healthy leaves reflect a lot of NIR radiation compared to dead or
stressed
leaves](attachment:../../img/earth-analytics/remote-sensing/spectral_vegetation_stress.png)
\> Image source: [Spectral signature literature review by
px39n](https://github.com/px39n/Awesome-Vegetation-Index)

Different species of plants reflect different spectral signatures, but
the *pattern* of the signatures are similar. NDVI compares the amount of
NIR reflectance to the amount of Red reflectance, thus accounting for
many of the species differences and isolating the health of the plant.
The formula for calculating NDVI is:

$$NDVI = \frac{(NIR - Red)}{(NIR + Red)}$$



# Import The Neccessary libraries


```python
# Install the development version of the earthpy package
!pip install git+https://github.com/earthlab/earthpy@apppears
```

    Collecting git+https://github.com/earthlab/earthpy@apppears
      Cloning https://github.com/earthlab/earthpy (to revision apppears) to /tmp/pip-req-build-dy2y0ra9
      Running command git clone --filter=blob:none --quiet https://github.com/earthlab/earthpy /tmp/pip-req-build-dy2y0ra9
      Running command git checkout -b apppears --track origin/apppears
      Switched to a new branch 'apppears'
      Branch 'apppears' set up to track remote branch 'apppears' from 'origin'.
      Resolved https://github.com/earthlab/earthpy to commit 20e0b17e563da17ba46dc32832cf4ed9173a7531
      Installing build dependencies ... [?25ldone
    [?25h  Getting requirements to build wheel ... [?25ldone
    [?25h  Preparing metadata (pyproject.toml) ... [?25ldone
    [?25hRequirement already satisfied: geopandas in /opt/conda/lib/python3.11/site-packages (from earthpy==0.10.0) (0.14.2)
    Requirement already satisfied: matplotlib>=2.0.0 in /opt/conda/lib/python3.11/site-packages (from earthpy==0.10.0) (3.8.4)
    Requirement already satisfied: numpy>=1.14.0 in /opt/conda/lib/python3.11/site-packages (from earthpy==0.10.0) (1.24.3)
    Requirement already satisfied: rasterio in /opt/conda/lib/python3.11/site-packages (from earthpy==0.10.0) (1.3.9)
    Requirement already satisfied: scikit-image in /opt/conda/lib/python3.11/site-packages (from earthpy==0.10.0) (0.22.0)
    Requirement already satisfied: requests in /opt/conda/lib/python3.11/site-packages (from earthpy==0.10.0) (2.31.0)
    Requirement already satisfied: keyring in /opt/conda/lib/python3.11/site-packages (from earthpy==0.10.0) (25.1.0)
    Requirement already satisfied: contourpy>=1.0.1 in /opt/conda/lib/python3.11/site-packages (from matplotlib>=2.0.0->earthpy==0.10.0) (1.2.0)
    Requirement already satisfied: cycler>=0.10 in /opt/conda/lib/python3.11/site-packages (from matplotlib>=2.0.0->earthpy==0.10.0) (0.11.0)
    Requirement already satisfied: fonttools>=4.22.0 in /opt/conda/lib/python3.11/site-packages (from matplotlib>=2.0.0->earthpy==0.10.0) (4.51.0)
    Requirement already satisfied: kiwisolver>=1.3.1 in /opt/conda/lib/python3.11/site-packages (from matplotlib>=2.0.0->earthpy==0.10.0) (1.4.4)
    Requirement already satisfied: packaging>=20.0 in /opt/conda/lib/python3.11/site-packages (from matplotlib>=2.0.0->earthpy==0.10.0) (24.0)
    Requirement already satisfied: pillow>=8 in /opt/conda/lib/python3.11/site-packages (from matplotlib>=2.0.0->earthpy==0.10.0) (10.3.0)
    Requirement already satisfied: pyparsing>=2.3.1 in /opt/conda/lib/python3.11/site-packages (from matplotlib>=2.0.0->earthpy==0.10.0) (3.0.9)
    Requirement already satisfied: python-dateutil>=2.7 in /opt/conda/lib/python3.11/site-packages (from matplotlib>=2.0.0->earthpy==0.10.0) (2.9.0)
    Requirement already satisfied: fiona>=1.8.21 in /opt/conda/lib/python3.11/site-packages (from geopandas->earthpy==0.10.0) (1.9.6)
    Requirement already satisfied: pandas>=1.4.0 in /opt/conda/lib/python3.11/site-packages (from geopandas->earthpy==0.10.0) (2.2.1)
    Requirement already satisfied: pyproj>=3.3.0 in /opt/conda/lib/python3.11/site-packages (from geopandas->earthpy==0.10.0) (3.6.1)
    Requirement already satisfied: shapely>=1.8.0 in /opt/conda/lib/python3.11/site-packages (from geopandas->earthpy==0.10.0) (2.0.4)
    Requirement already satisfied: jaraco.classes in /opt/conda/lib/python3.11/site-packages (from keyring->earthpy==0.10.0) (3.4.0)
    Requirement already satisfied: jaraco.functools in /opt/conda/lib/python3.11/site-packages (from keyring->earthpy==0.10.0) (4.0.1)
    Requirement already satisfied: jaraco.context in /opt/conda/lib/python3.11/site-packages (from keyring->earthpy==0.10.0) (5.3.0)
    Requirement already satisfied: importlib-metadata>=4.11.4 in /opt/conda/lib/python3.11/site-packages (from keyring->earthpy==0.10.0) (7.1.0)
    Requirement already satisfied: SecretStorage>=3.2 in /opt/conda/lib/python3.11/site-packages (from keyring->earthpy==0.10.0) (3.3.3)
    Requirement already satisfied: jeepney>=0.4.2 in /opt/conda/lib/python3.11/site-packages (from keyring->earthpy==0.10.0) (0.8.0)
    Requirement already satisfied: affine in /opt/conda/lib/python3.11/site-packages (from rasterio->earthpy==0.10.0) (2.3.0)
    Requirement already satisfied: attrs in /opt/conda/lib/python3.11/site-packages (from rasterio->earthpy==0.10.0) (23.2.0)
    Requirement already satisfied: certifi in /opt/conda/lib/python3.11/site-packages (from rasterio->earthpy==0.10.0) (2024.2.2)
    Requirement already satisfied: click>=4.0 in /opt/conda/lib/python3.11/site-packages (from rasterio->earthpy==0.10.0) (8.1.7)
    Requirement already satisfied: cligj>=0.5 in /opt/conda/lib/python3.11/site-packages (from rasterio->earthpy==0.10.0) (0.7.2)
    Requirement already satisfied: snuggs>=1.4.1 in /opt/conda/lib/python3.11/site-packages (from rasterio->earthpy==0.10.0) (1.4.7)
    Requirement already satisfied: click-plugins in /opt/conda/lib/python3.11/site-packages (from rasterio->earthpy==0.10.0) (1.1.1)
    Requirement already satisfied: setuptools in /opt/conda/lib/python3.11/site-packages (from rasterio->earthpy==0.10.0) (69.5.1)
    Requirement already satisfied: charset-normalizer<4,>=2 in /opt/conda/lib/python3.11/site-packages (from requests->earthpy==0.10.0) (3.3.2)
    Requirement already satisfied: idna<4,>=2.5 in /opt/conda/lib/python3.11/site-packages (from requests->earthpy==0.10.0) (3.7)
    Requirement already satisfied: urllib3<3,>=1.21.1 in /opt/conda/lib/python3.11/site-packages (from requests->earthpy==0.10.0) (2.2.1)
    Requirement already satisfied: scipy>=1.8 in /opt/conda/lib/python3.11/site-packages (from scikit-image->earthpy==0.10.0) (1.12.0)
    Requirement already satisfied: networkx>=2.8 in /opt/conda/lib/python3.11/site-packages (from scikit-image->earthpy==0.10.0) (3.1)
    Requirement already satisfied: imageio>=2.27 in /opt/conda/lib/python3.11/site-packages (from scikit-image->earthpy==0.10.0) (2.33.1)
    Requirement already satisfied: tifffile>=2022.8.12 in /opt/conda/lib/python3.11/site-packages (from scikit-image->earthpy==0.10.0) (2023.4.12)
    Requirement already satisfied: lazy_loader>=0.3 in /opt/conda/lib/python3.11/site-packages (from scikit-image->earthpy==0.10.0) (0.3)
    Requirement already satisfied: six in /opt/conda/lib/python3.11/site-packages (from fiona>=1.8.21->geopandas->earthpy==0.10.0) (1.16.0)
    Requirement already satisfied: zipp>=0.5 in /opt/conda/lib/python3.11/site-packages (from importlib-metadata>=4.11.4->keyring->earthpy==0.10.0) (3.17.0)
    Requirement already satisfied: pytz>=2020.1 in /opt/conda/lib/python3.11/site-packages (from pandas>=1.4.0->geopandas->earthpy==0.10.0) (2024.1)
    Requirement already satisfied: tzdata>=2022.7 in /opt/conda/lib/python3.11/site-packages (from pandas>=1.4.0->geopandas->earthpy==0.10.0) (2023.3)
    Requirement already satisfied: cryptography>=2.0 in /opt/conda/lib/python3.11/site-packages (from SecretStorage>=3.2->keyring->earthpy==0.10.0) (42.0.5)
    Requirement already satisfied: more-itertools in /opt/conda/lib/python3.11/site-packages (from jaraco.classes->keyring->earthpy==0.10.0) (10.2.0)
    Requirement already satisfied: backports.tarfile in /opt/conda/lib/python3.11/site-packages (from jaraco.context->keyring->earthpy==0.10.0) (1.1.1)
    Requirement already satisfied: cffi>=1.12 in /opt/conda/lib/python3.11/site-packages (from cryptography>=2.0->SecretStorage>=3.2->keyring->earthpy==0.10.0) (1.16.0)
    Requirement already satisfied: pycparser in /opt/conda/lib/python3.11/site-packages (from cffi>=1.12->cryptography>=2.0->SecretStorage>=3.2->keyring->earthpy==0.10.0) (2.22)



```python
import getpass
import json
import os
import pathlib
from glob import glob
import matplotlib.pyplot as plt

# Library to work with tabular data
import pandas as pd

# Library to work with vector data
import geopandas as gpd

import earthpy.appeears as eaapp
import hvplot.pandas
import hvplot.xarray
import rioxarray as rxr
import xarray as xr
```






<style>*[data-root-id],
*[data-root-id] > * {
  box-sizing: border-box;
  font-family: var(--jp-ui-font-family);
  font-size: var(--jp-ui-font-size1);
  color: var(--vscode-editor-foreground, var(--jp-ui-font-color1));
}

/* Override VSCode background color */
.cell-output-ipywidget-background:has(
    > .cell-output-ipywidget-background > .lm-Widget > *[data-root-id]
  ),
.cell-output-ipywidget-background:has(> .lm-Widget > *[data-root-id]) {
  background-color: transparent !important;
}
</style>



<div id='p1002'>
  <div id="ecc25a1e-92ab-41ee-a9b1-70b7e7dc64e4" data-root-id="p1002" style="display: contents;"></div>
</div>
<script type="application/javascript">(function(root) {
  var docs_json = {"76afb570-c319-4101-993f-b2386d0ab3cb":{"version":"3.3.4","title":"Bokeh Application","roots":[{"type":"object","name":"panel.models.browser.BrowserInfo","id":"p1002"},{"type":"object","name":"panel.models.comm_manager.CommManager","id":"p1003","attributes":{"plot_id":"p1002","comm_id":"c8be359efad241f280a67363b6df198e","client_comm_id":"64fb4a1224ae47e9af8b3da665d67137"}}],"defs":[{"type":"model","name":"ReactiveHTML1"},{"type":"model","name":"FlexBox1","properties":[{"name":"align_content","kind":"Any","default":"flex-start"},{"name":"align_items","kind":"Any","default":"flex-start"},{"name":"flex_direction","kind":"Any","default":"row"},{"name":"flex_wrap","kind":"Any","default":"wrap"},{"name":"justify_content","kind":"Any","default":"flex-start"}]},{"type":"model","name":"FloatPanel1","properties":[{"name":"config","kind":"Any","default":{"type":"map"}},{"name":"contained","kind":"Any","default":true},{"name":"position","kind":"Any","default":"right-top"},{"name":"offsetx","kind":"Any","default":null},{"name":"offsety","kind":"Any","default":null},{"name":"theme","kind":"Any","default":"primary"},{"name":"status","kind":"Any","default":"normalized"}]},{"type":"model","name":"GridStack1","properties":[{"name":"mode","kind":"Any","default":"warn"},{"name":"ncols","kind":"Any","default":null},{"name":"nrows","kind":"Any","default":null},{"name":"allow_resize","kind":"Any","default":true},{"name":"allow_drag","kind":"Any","default":true},{"name":"state","kind":"Any","default":[]}]},{"type":"model","name":"drag1","properties":[{"name":"slider_width","kind":"Any","default":5},{"name":"slider_color","kind":"Any","default":"black"},{"name":"value","kind":"Any","default":50}]},{"type":"model","name":"click1","properties":[{"name":"terminal_output","kind":"Any","default":""},{"name":"debug_name","kind":"Any","default":""},{"name":"clears","kind":"Any","default":0}]},{"type":"model","name":"copy_to_clipboard1","properties":[{"name":"fill","kind":"Any","default":"none"},{"name":"value","kind":"Any","default":null}]},{"type":"model","name":"FastWrapper1","properties":[{"name":"object","kind":"Any","default":null},{"name":"style","kind":"Any","default":null}]},{"type":"model","name":"NotificationAreaBase1","properties":[{"name":"js_events","kind":"Any","default":{"type":"map"}},{"name":"position","kind":"Any","default":"bottom-right"},{"name":"_clear","kind":"Any","default":0}]},{"type":"model","name":"NotificationArea1","properties":[{"name":"js_events","kind":"Any","default":{"type":"map"}},{"name":"notifications","kind":"Any","default":[]},{"name":"position","kind":"Any","default":"bottom-right"},{"name":"_clear","kind":"Any","default":0},{"name":"types","kind":"Any","default":[{"type":"map","entries":[["type","warning"],["background","#ffc107"],["icon",{"type":"map","entries":[["className","fas fa-exclamation-triangle"],["tagName","i"],["color","white"]]}]]},{"type":"map","entries":[["type","info"],["background","#007bff"],["icon",{"type":"map","entries":[["className","fas fa-info-circle"],["tagName","i"],["color","white"]]}]]}]}]},{"type":"model","name":"Notification","properties":[{"name":"background","kind":"Any","default":null},{"name":"duration","kind":"Any","default":3000},{"name":"icon","kind":"Any","default":null},{"name":"message","kind":"Any","default":""},{"name":"notification_type","kind":"Any","default":null},{"name":"_destroyed","kind":"Any","default":false}]},{"type":"model","name":"TemplateActions1","properties":[{"name":"open_modal","kind":"Any","default":0},{"name":"close_modal","kind":"Any","default":0}]},{"type":"model","name":"BootstrapTemplateActions1","properties":[{"name":"open_modal","kind":"Any","default":0},{"name":"close_modal","kind":"Any","default":0}]},{"type":"model","name":"MaterialTemplateActions1","properties":[{"name":"open_modal","kind":"Any","default":0},{"name":"close_modal","kind":"Any","default":0}]}]}};
  var render_items = [{"docid":"76afb570-c319-4101-993f-b2386d0ab3cb","roots":{"p1002":"ecc25a1e-92ab-41ee-a9b1-70b7e7dc64e4"},"root_ids":["p1002"]}];
  var docs = Object.values(docs_json)
  if (!docs) {
    return
  }
  const py_version = docs[0].version.replace('rc', '-rc.').replace('.dev', '-dev.')
  function embed_document(root) {
    var Bokeh = get_bokeh(root)
    Bokeh.embed.embed_items_notebook(docs_json, render_items);
    for (const render_item of render_items) {
      for (const root_id of render_item.root_ids) {
	const id_el = document.getElementById(root_id)
	if (id_el.children.length && (id_el.children[0].className === 'bk-root')) {
	  const root_el = id_el.children[0]
	  root_el.id = root_el.id + '-rendered'
	}
      }
    }
  }
  function get_bokeh(root) {
    if (root.Bokeh === undefined) {
      return null
    } else if (root.Bokeh.version !== py_version) {
      if (root.Bokeh.versions === undefined || !root.Bokeh.versions.has(py_version)) {
	return null
      }
      return root.Bokeh.versions.get(py_version);
    } else if (root.Bokeh.version === py_version) {
      return root.Bokeh
    }
    return null
  }
  function is_loaded(root) {
    var Bokeh = get_bokeh(root)
    return (Bokeh != null && Bokeh.Panel !== undefined)
  }
  if (is_loaded(root)) {
    embed_document(root);
  } else {
    var attempts = 0;
    var timer = setInterval(function(root) {
      if (is_loaded(root)) {
        clearInterval(timer);
        embed_document(root);
      } else if (document.readyState == "complete") {
        attempts++;
        if (attempts > 200) {
          clearInterval(timer);
	  var Bokeh = get_bokeh(root)
	  if (Bokeh == null || Bokeh.Panel == null) {
            console.warn("Panel: ERROR: Unable to run Panel code because Bokeh or Panel library is missing");
	  } else {
	    console.warn("Panel: WARNING: Attempting to render but not all required libraries could be resolved.")
	    embed_document(root)
	  }
        }
      }
    }, 25, root)
  }
})(window);</script>



```python
data_dir = os.path.join(pathlib.Path.home(), 'cameron-peak-data')
# Make the data directory
os.makedirs(data_dir, exist_ok=True)


data_dir
```




    '/home/jovyan/cameron-peak-data'



## Study Area: Cameron Peak Fire Boundary

### Earth Data Science data formats

In Earth Data Science, we get data in three main formats:

| Data type   | Descriptions                                                              | Common file formats                                                                                              | Python type            |
|----------------|------------------|------------------------|----------------|
| Time Series | The same data points (e.g. streamflow) collected multiple times over time | Tabular formats (e.g. .csv, or .xlsx)                                                                            | pandas DataFrame       |
| Vector      | Points, lines, and areas (with coordinates)                               | Shapefile (often an archive like a `.zip` file because a Shapefile is actually a collection of at least 3 files) | geopandas GeoDataFrame |
| Raster      | Evenly spaced spatial grid (with coordinates)                             | GeoTIFF (`.tif`), NetCDF (`.nc`), HDF (`.hdf`)                                                                   | rioxarray DataArray    |




```python
# Download the Cameron Peak fire boundary
cameron_peak_url = ("https://services3.arcgis.com/T4QMspbfLg3qTGWY/"
                    "arcgis/rest/services/WFIGS_Interagency_Perimeters/"
                    "FeatureServer/0/"
                    "query?where=poly_IncidentName%20%3D%20'CAMERON%20PEAK'&"
                    "outFields=*&outSR=4326&f=json")
cameron_peak_url

# Read File with Geopandas
cameron_peak_gdf = gpd.read_file(cameron_peak_url)
cameron_peak_gdf
```

    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")
    /opt/conda/lib/python3.11/site-packages/geopandas/io/file.py:399: FutureWarning: errors='ignore' is deprecated and will raise in a future version. Use to_datetime without passing `errors` and catch exceptions explicitly instead
      as_dt = pd.to_datetime(df[k], errors="ignore")





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>OBJECTID</th>
      <th>poly_SourceOID</th>
      <th>poly_IncidentName</th>
      <th>poly_FeatureCategory</th>
      <th>poly_MapMethod</th>
      <th>poly_GISAcres</th>
      <th>poly_CreateDate</th>
      <th>poly_DateCurrent</th>
      <th>poly_PolygonDateTime</th>
      <th>poly_IRWINID</th>
      <th>...</th>
      <th>attr_ModifiedOnDateTime_dt</th>
      <th>attr_Source</th>
      <th>attr_IsCpxChild</th>
      <th>attr_CpxName</th>
      <th>attr_CpxID</th>
      <th>attr_SourceGlobalID</th>
      <th>GlobalID</th>
      <th>Shape__Area</th>
      <th>Shape__Length</th>
      <th>geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>14659</td>
      <td>10393</td>
      <td>Cameron Peak</td>
      <td>Wildfire Final Fire Perimeter</td>
      <td>Infrared Image</td>
      <td>208913.3</td>
      <td>NaT</td>
      <td>2023-03-14 15:13:42.810000+00:00</td>
      <td>2020-10-06 21:30:00+00:00</td>
      <td>{53741A13-D269-4CD5-AF91-02E094B944DA}</td>
      <td>...</td>
      <td>2021-12-06 19:48:12.647000+00:00</td>
      <td>FODR</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>{5F5EC9BA-5F85-495B-8B97-1E0969A1434E}</td>
      <td>e8e4ffd4-db84-4d47-bd32-d4b0a8381fff</td>
      <td>0.089957</td>
      <td>5.541765</td>
      <td>MULTIPOLYGON (((-105.88333 40.54598, -105.8837...</td>
    </tr>
  </tbody>
</table>
<p>1 rows × 114 columns</p>
</div>



### Site Map

We always want to create a site map when working with geospatial data.
This helps us see that we’re looking at the right location, and learn
something about the context of the analysis.




```python
# Plot the Cameron Peak Fire boundary
cameron_peak_gdf[["geometry"]].explore()
 
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe srcdoc="&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;

    &lt;meta http-equiv=&quot;content-type&quot; content=&quot;text/html; charset=UTF-8&quot; /&gt;

        &lt;script&gt;
            L_NO_TOUCH = false;
            L_DISABLE_3D = false;
        &lt;/script&gt;

    &lt;style&gt;html, body {width: 100%;height: 100%;margin: 0;padding: 0;}&lt;/style&gt;
    &lt;style&gt;#map {position:absolute;top:0;bottom:0;right:0;left:0;}&lt;/style&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://code.jquery.com/jquery-1.12.4.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/js/bootstrap.bundle.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.2.0/css/all.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/python-visualization/folium/folium/templates/leaflet.awesome.rotate.min.css&quot;/&gt;

            &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width,
                initial-scale=1.0, maximum-scale=1.0, user-scalable=no&quot; /&gt;
            &lt;style&gt;
                #map_c9a3e647324bc31120c1a7f6146bbe69 {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
                .leaflet-container { font-size: 1rem; }
            &lt;/style&gt;


                    &lt;style&gt;
                        .foliumtooltip {

                        }
                       .foliumtooltip table{
                            margin: auto;
                        }
                        .foliumtooltip tr{
                            text-align: left;
                        }
                        .foliumtooltip th{
                            padding: 2px; padding-right: 8px;
                        }
                    &lt;/style&gt;

&lt;/head&gt;
&lt;body&gt;


            &lt;div class=&quot;folium-map&quot; id=&quot;map_c9a3e647324bc31120c1a7f6146bbe69&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;


            var map_c9a3e647324bc31120c1a7f6146bbe69 = L.map(
                &quot;map_c9a3e647324bc31120c1a7f6146bbe69&quot;,
                {
                    center: [40.619900926500094, -105.5513693685],
                    crs: L.CRS.EPSG3857,
                    zoom: 10,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );
            L.control.scale().addTo(map_c9a3e647324bc31120c1a7f6146bbe69);





            var tile_layer_bbc63daf642b184ceec6c307764fd8c1 = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca target=\&quot;_blank\&quot; href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca target=\&quot;_blank\&quot; href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_c9a3e647324bc31120c1a7f6146bbe69);


            map_c9a3e647324bc31120c1a7f6146bbe69.fitBounds(
                [[40.4613389790001, -105.903706556], [40.7784628740001, -105.199032181]],
                {}
            );


        function geo_json_077a76032abc972d7c33aab94f7538a6_styler(feature) {
            switch(feature.id) {
                default:
                    return {&quot;fillOpacity&quot;: 0.5, &quot;weight&quot;: 2};
            }
        }
        function geo_json_077a76032abc972d7c33aab94f7538a6_highlighter(feature) {
            switch(feature.id) {
                default:
                    return {&quot;fillOpacity&quot;: 0.75};
            }
        }
        function geo_json_077a76032abc972d7c33aab94f7538a6_pointToLayer(feature, latlng) {
            var opts = {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 2, &quot;stroke&quot;: true, &quot;weight&quot;: 3};

            let style = geo_json_077a76032abc972d7c33aab94f7538a6_styler(feature)
            Object.assign(opts, style)

            return new L.CircleMarker(latlng, opts)
        }

        function geo_json_077a76032abc972d7c33aab94f7538a6_onEachFeature(feature, layer) {
            layer.on({
                mouseout: function(e) {
                    if(typeof e.target.setStyle === &quot;function&quot;){
                        geo_json_077a76032abc972d7c33aab94f7538a6.resetStyle(e.target);
                    }
                },
                mouseover: function(e) {
                    if(typeof e.target.setStyle === &quot;function&quot;){
                        const highlightStyle = geo_json_077a76032abc972d7c33aab94f7538a6_highlighter(e.target.feature)
                        e.target.setStyle(highlightStyle);
                    }
                },
            });
        };
        var geo_json_077a76032abc972d7c33aab94f7538a6 = L.geoJson(null, {
                onEachFeature: geo_json_077a76032abc972d7c33aab94f7538a6_onEachFeature,

                style: geo_json_077a76032abc972d7c33aab94f7538a6_styler,
                pointToLayer: geo_json_077a76032abc972d7c33aab94f7538a6_pointToLayer
        });

        function geo_json_077a76032abc972d7c33aab94f7538a6_add (data) {
            geo_json_077a76032abc972d7c33aab94f7538a6
                .addData(data)
                .addTo(map_c9a3e647324bc31120c1a7f6146bbe69);
        }



    geo_json_077a76032abc972d7c33aab94f7538a6.bindTooltip(
    function(layer){
    let div = L.DomUtil.create(&#x27;div&#x27;);

    return div
    }
    ,{&quot;className&quot;: &quot;foliumtooltip&quot;, &quot;sticky&quot;: true});

&lt;/script&gt;
&lt;/html&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>



## Exploring the AppEEARS API for NASA Earthdata access

 MODIS is a multispectral instrument that measures Red and
NIR data (and so can be used for NDVI). There are two MODIS sensors on
two different platforms: satellites Terra and Aqua.

> **<i class="fa fa-solid fa-glasses fa-large" aria-label="glasses"></i>
> Read more**
>
> [Learn more about MODIS datasets and the science they
> support](https://modis.gsfc.nasa.gov/)

Since we’re asking for a special download that only covers our study
area, we can’t just find a link to the data - we have to negotiate with
the data server. We’re doing this using the
[APPEEARS](https://appeears.earthdatacloud.nasa.gov/api/) API
(Application Programming Interface). The API makes it possible for you
to request data using code. You can use code from the `earthpy` library
to handle the API request.




```python
# Initialize AppeearsDownloader for MODIS NDVI data
ndvi_downloader = eaapp.AppeearsDownloader(
    download_key='cp-ndvi',
    ea_dir=data_dir,
    product='MOD13Q1.061',
    layer='_250m_16_days_NDVI',
    start_date="07-01",
    end_date="07-31",
    recurring=True,
    year_range=[2018, 2023],
    polygon= cameron_peak_gdf
)

ndvi_downloader.download_files(cache=True)
```

## Putting it together: Working with multi-file raster datasets in Python




```python
# Get a list of NDVI tif file paths
ndvi_tif_paths = sorted(glob(os.path.join(data_dir, 'cp-ndvi', '*', '*NDVI*.tif')))
len(ndvi_tif_paths)
```




    18



### Repeating tasks in Python

For each file, you need to:

-   Load the file in using the `rioxarray` library
-   Get the date from the file name
-   Add the date as a dimension coordinate
-   Give your data variable a name
-   Divide by the scale factor of 10000

You don’t want to write out the code for each file! That’s a recipe for
copy pasta. Luckily, Python has tools for doing similar tasks
repeatedly. In this case, you’ll use one called a `for` loop.

There’s some code below that uses a `for` loop in what is called an
**accumulation pattern** to process each file. That means that you will
save the results of your processing to a list each time you process the
files, and then merge all the arrays in the list.




```python
scale_factor = 10000
doy_start = -19
doy_end = -12
```


```python
ndvi_das = []
for ndvi_tif_path in ndvi_tif_paths:
    # Get date from file name
    doy = ndvi_tif_path[doy_start:doy_end]
    date = pd.to_datetime(doy, format='%Y%j')

    # Open dataset
    da = rxr.open_rasterio(ndvi_tif_path, masked=True).squeeze()

    # Add date dimension and clean up metadata
    da = da.assign_coords({'date': date})
    da = da.expand_dims({'date': 1})
    da.name = 'NDVI'

    # Multiple by scale factor
    da = da / scale_factor

    # Prepare for concatenation
    ndvi_das.append(da)

len(ndvi_das)
```




    18




```python
# Assuming ndvi_das is a list of DataArray objects that you want to combine
ndvi_da= xr.combine_by_coords(ndvi_das, coords=['date'])
ndvi_da

```




<div><svg style="position: absolute; width: 0; height: 0; overflow: hidden">
<defs>
<symbol id="icon-database" viewBox="0 0 32 32">
<path d="M16 0c-8.837 0-16 2.239-16 5v4c0 2.761 7.163 5 16 5s16-2.239 16-5v-4c0-2.761-7.163-5-16-5z"></path>
<path d="M16 17c-8.837 0-16-2.239-16-5v6c0 2.761 7.163 5 16 5s16-2.239 16-5v-6c0 2.761-7.163 5-16 5z"></path>
<path d="M16 26c-8.837 0-16-2.239-16-5v6c0 2.761 7.163 5 16 5s16-2.239 16-5v-6c0 2.761-7.163 5-16 5z"></path>
</symbol>
<symbol id="icon-file-text2" viewBox="0 0 32 32">
<path d="M28.681 7.159c-0.694-0.947-1.662-2.053-2.724-3.116s-2.169-2.030-3.116-2.724c-1.612-1.182-2.393-1.319-2.841-1.319h-15.5c-1.378 0-2.5 1.121-2.5 2.5v27c0 1.378 1.122 2.5 2.5 2.5h23c1.378 0 2.5-1.122 2.5-2.5v-19.5c0-0.448-0.137-1.23-1.319-2.841zM24.543 5.457c0.959 0.959 1.712 1.825 2.268 2.543h-4.811v-4.811c0.718 0.556 1.584 1.309 2.543 2.268zM28 29.5c0 0.271-0.229 0.5-0.5 0.5h-23c-0.271 0-0.5-0.229-0.5-0.5v-27c0-0.271 0.229-0.5 0.5-0.5 0 0 15.499-0 15.5 0v7c0 0.552 0.448 1 1 1h7v19.5z"></path>
<path d="M23 26h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
<path d="M23 22h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
<path d="M23 18h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
</symbol>
</defs>
</svg>
<style>/* CSS stylesheet for displaying xarray objects in jupyterlab.
 *
 */

:root {
  --xr-font-color0: var(--jp-content-font-color0, rgba(0, 0, 0, 1));
  --xr-font-color2: var(--jp-content-font-color2, rgba(0, 0, 0, 0.54));
  --xr-font-color3: var(--jp-content-font-color3, rgba(0, 0, 0, 0.38));
  --xr-border-color: var(--jp-border-color2, #e0e0e0);
  --xr-disabled-color: var(--jp-layout-color3, #bdbdbd);
  --xr-background-color: var(--jp-layout-color0, white);
  --xr-background-color-row-even: var(--jp-layout-color1, white);
  --xr-background-color-row-odd: var(--jp-layout-color2, #eeeeee);
}

html[theme=dark],
body[data-theme=dark],
body.vscode-dark {
  --xr-font-color0: rgba(255, 255, 255, 1);
  --xr-font-color2: rgba(255, 255, 255, 0.54);
  --xr-font-color3: rgba(255, 255, 255, 0.38);
  --xr-border-color: #1F1F1F;
  --xr-disabled-color: #515151;
  --xr-background-color: #111111;
  --xr-background-color-row-even: #111111;
  --xr-background-color-row-odd: #313131;
}

.xr-wrap {
  display: block !important;
  min-width: 300px;
  max-width: 700px;
}

.xr-text-repr-fallback {
  /* fallback to plain text repr when CSS is not injected (untrusted notebook) */
  display: none;
}

.xr-header {
  padding-top: 6px;
  padding-bottom: 6px;
  margin-bottom: 4px;
  border-bottom: solid 1px var(--xr-border-color);
}

.xr-header > div,
.xr-header > ul {
  display: inline;
  margin-top: 0;
  margin-bottom: 0;
}

.xr-obj-type,
.xr-array-name {
  margin-left: 2px;
  margin-right: 10px;
}

.xr-obj-type {
  color: var(--xr-font-color2);
}

.xr-sections {
  padding-left: 0 !important;
  display: grid;
  grid-template-columns: 150px auto auto 1fr 20px 20px;
}

.xr-section-item {
  display: contents;
}

.xr-section-item input {
  display: none;
}

.xr-section-item input + label {
  color: var(--xr-disabled-color);
}

.xr-section-item input:enabled + label {
  cursor: pointer;
  color: var(--xr-font-color2);
}

.xr-section-item input:enabled + label:hover {
  color: var(--xr-font-color0);
}

.xr-section-summary {
  grid-column: 1;
  color: var(--xr-font-color2);
  font-weight: 500;
}

.xr-section-summary > span {
  display: inline-block;
  padding-left: 0.5em;
}

.xr-section-summary-in:disabled + label {
  color: var(--xr-font-color2);
}

.xr-section-summary-in + label:before {
  display: inline-block;
  content: '►';
  font-size: 11px;
  width: 15px;
  text-align: center;
}

.xr-section-summary-in:disabled + label:before {
  color: var(--xr-disabled-color);
}

.xr-section-summary-in:checked + label:before {
  content: '▼';
}

.xr-section-summary-in:checked + label > span {
  display: none;
}

.xr-section-summary,
.xr-section-inline-details {
  padding-top: 4px;
  padding-bottom: 4px;
}

.xr-section-inline-details {
  grid-column: 2 / -1;
}

.xr-section-details {
  display: none;
  grid-column: 1 / -1;
  margin-bottom: 5px;
}

.xr-section-summary-in:checked ~ .xr-section-details {
  display: contents;
}

.xr-array-wrap {
  grid-column: 1 / -1;
  display: grid;
  grid-template-columns: 20px auto;
}

.xr-array-wrap > label {
  grid-column: 1;
  vertical-align: top;
}

.xr-preview {
  color: var(--xr-font-color3);
}

.xr-array-preview,
.xr-array-data {
  padding: 0 5px !important;
  grid-column: 2;
}

.xr-array-data,
.xr-array-in:checked ~ .xr-array-preview {
  display: none;
}

.xr-array-in:checked ~ .xr-array-data,
.xr-array-preview {
  display: inline-block;
}

.xr-dim-list {
  display: inline-block !important;
  list-style: none;
  padding: 0 !important;
  margin: 0;
}

.xr-dim-list li {
  display: inline-block;
  padding: 0;
  margin: 0;
}

.xr-dim-list:before {
  content: '(';
}

.xr-dim-list:after {
  content: ')';
}

.xr-dim-list li:not(:last-child):after {
  content: ',';
  padding-right: 5px;
}

.xr-has-index {
  font-weight: bold;
}

.xr-var-list,
.xr-var-item {
  display: contents;
}

.xr-var-item > div,
.xr-var-item label,
.xr-var-item > .xr-var-name span {
  background-color: var(--xr-background-color-row-even);
  margin-bottom: 0;
}

.xr-var-item > .xr-var-name:hover span {
  padding-right: 5px;
}

.xr-var-list > li:nth-child(odd) > div,
.xr-var-list > li:nth-child(odd) > label,
.xr-var-list > li:nth-child(odd) > .xr-var-name span {
  background-color: var(--xr-background-color-row-odd);
}

.xr-var-name {
  grid-column: 1;
}

.xr-var-dims {
  grid-column: 2;
}

.xr-var-dtype {
  grid-column: 3;
  text-align: right;
  color: var(--xr-font-color2);
}

.xr-var-preview {
  grid-column: 4;
}

.xr-index-preview {
  grid-column: 2 / 5;
  color: var(--xr-font-color2);
}

.xr-var-name,
.xr-var-dims,
.xr-var-dtype,
.xr-preview,
.xr-attrs dt {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  padding-right: 10px;
}

.xr-var-name:hover,
.xr-var-dims:hover,
.xr-var-dtype:hover,
.xr-attrs dt:hover {
  overflow: visible;
  width: auto;
  z-index: 1;
}

.xr-var-attrs,
.xr-var-data,
.xr-index-data {
  display: none;
  background-color: var(--xr-background-color) !important;
  padding-bottom: 5px !important;
}

.xr-var-attrs-in:checked ~ .xr-var-attrs,
.xr-var-data-in:checked ~ .xr-var-data,
.xr-index-data-in:checked ~ .xr-index-data {
  display: block;
}

.xr-var-data > table {
  float: right;
}

.xr-var-name span,
.xr-var-data,
.xr-index-name div,
.xr-index-data,
.xr-attrs {
  padding-left: 25px !important;
}

.xr-attrs,
.xr-var-attrs,
.xr-var-data,
.xr-index-data {
  grid-column: 1 / -1;
}

dl.xr-attrs {
  padding: 0;
  margin: 0;
  display: grid;
  grid-template-columns: 125px auto;
}

.xr-attrs dt,
.xr-attrs dd {
  padding: 0;
  margin: 0;
  float: left;
  padding-right: 10px;
  width: auto;
}

.xr-attrs dt {
  font-weight: normal;
  grid-column: 1;
}

.xr-attrs dt:hover span {
  display: inline-block;
  background: var(--xr-background-color);
  padding-right: 10px;
}

.xr-attrs dd {
  grid-column: 2;
  white-space: pre-wrap;
  word-break: break-all;
}

.xr-icon-database,
.xr-icon-file-text2,
.xr-no-icon {
  display: inline-block;
  vertical-align: middle;
  width: 1em;
  height: 1.5em !important;
  stroke-width: 0;
  stroke: currentColor;
  fill: currentColor;
}
</style><pre class='xr-text-repr-fallback'>&lt;xarray.Dataset&gt;
Dimensions:      (x: 339, y: 153, date: 18)
Coordinates:
    band         int64 1
  * x            (x) float64 -105.9 -105.9 -105.9 ... -105.2 -105.2 -105.2
  * y            (y) float64 40.78 40.78 40.77 40.77 ... 40.47 40.47 40.46 40.46
    spatial_ref  int64 0
  * date         (date) datetime64[ns] 2018-06-26 2018-07-12 ... 2023-07-28
Data variables:
    NDVI         (date, y, x) float32 0.5672 0.5711 0.6027 ... 0.6327 0.6327</pre><div class='xr-wrap' style='display:none'><div class='xr-header'><div class='xr-obj-type'>xarray.Dataset</div></div><ul class='xr-sections'><li class='xr-section-item'><input id='section-c31c6e97-d346-43ac-8c91-6907b098a42d' class='xr-section-summary-in' type='checkbox' disabled ><label for='section-c31c6e97-d346-43ac-8c91-6907b098a42d' class='xr-section-summary'  title='Expand/collapse section'>Dimensions:</label><div class='xr-section-inline-details'><ul class='xr-dim-list'><li><span class='xr-has-index'>x</span>: 339</li><li><span class='xr-has-index'>y</span>: 153</li><li><span class='xr-has-index'>date</span>: 18</li></ul></div><div class='xr-section-details'></div></li><li class='xr-section-item'><input id='section-76198fb0-d80e-4d46-a02c-df951789b78f' class='xr-section-summary-in' type='checkbox'  checked><label for='section-76198fb0-d80e-4d46-a02c-df951789b78f' class='xr-section-summary' >Coordinates: <span>(5)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-var-name'><span>band</span></div><div class='xr-var-dims'>()</div><div class='xr-var-dtype'>int64</div><div class='xr-var-preview xr-preview'>1</div><input id='attrs-8413e1cb-6155-4bc3-8ac4-25e6339629c4' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-8413e1cb-6155-4bc3-8ac4-25e6339629c4' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-68cdc4ae-a844-45b5-89c9-95cf30498b85' class='xr-var-data-in' type='checkbox'><label for='data-68cdc4ae-a844-45b5-89c9-95cf30498b85' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array(1)</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>x</span></div><div class='xr-var-dims'>(x)</div><div class='xr-var-dtype'>float64</div><div class='xr-var-preview xr-preview'>-105.9 -105.9 ... -105.2 -105.2</div><input id='attrs-6e6feb87-93c0-4603-8309-e5a3fb535613' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-6e6feb87-93c0-4603-8309-e5a3fb535613' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-3ecc513f-2ad2-4cb1-b07b-f02cc8fd2cd1' class='xr-var-data-in' type='checkbox'><label for='data-3ecc513f-2ad2-4cb1-b07b-f02cc8fd2cd1' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([-105.903125, -105.901042, -105.898958, ..., -105.203125, -105.201042,
       -105.198958])</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>y</span></div><div class='xr-var-dims'>(y)</div><div class='xr-var-dtype'>float64</div><div class='xr-var-preview xr-preview'>40.78 40.78 40.77 ... 40.46 40.46</div><input id='attrs-ff121332-67ef-407a-aef9-94aeaadeaae0' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-ff121332-67ef-407a-aef9-94aeaadeaae0' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-0432255e-9f8e-490f-9e3d-85e4aa322c3c' class='xr-var-data-in' type='checkbox'><label for='data-0432255e-9f8e-490f-9e3d-85e4aa322c3c' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([40.778125, 40.776042, 40.773958, 40.771875, 40.769792, 40.767708,
       40.765625, 40.763542, 40.761458, 40.759375, 40.757292, 40.755208,
       40.753125, 40.751042, 40.748958, 40.746875, 40.744792, 40.742708,
       40.740625, 40.738542, 40.736458, 40.734375, 40.732292, 40.730208,
       40.728125, 40.726042, 40.723958, 40.721875, 40.719792, 40.717708,
       40.715625, 40.713542, 40.711458, 40.709375, 40.707292, 40.705208,
       40.703125, 40.701042, 40.698958, 40.696875, 40.694792, 40.692708,
       40.690625, 40.688542, 40.686458, 40.684375, 40.682292, 40.680208,
       40.678125, 40.676042, 40.673958, 40.671875, 40.669792, 40.667708,
       40.665625, 40.663542, 40.661458, 40.659375, 40.657292, 40.655208,
       40.653125, 40.651042, 40.648958, 40.646875, 40.644792, 40.642708,
       40.640625, 40.638542, 40.636458, 40.634375, 40.632292, 40.630208,
       40.628125, 40.626042, 40.623958, 40.621875, 40.619792, 40.617708,
       40.615625, 40.613542, 40.611458, 40.609375, 40.607292, 40.605208,
       40.603125, 40.601042, 40.598958, 40.596875, 40.594792, 40.592708,
       40.590625, 40.588542, 40.586458, 40.584375, 40.582292, 40.580208,
       40.578125, 40.576042, 40.573958, 40.571875, 40.569792, 40.567708,
       40.565625, 40.563542, 40.561458, 40.559375, 40.557292, 40.555208,
       40.553125, 40.551042, 40.548958, 40.546875, 40.544792, 40.542708,
       40.540625, 40.538542, 40.536458, 40.534375, 40.532292, 40.530208,
       40.528125, 40.526042, 40.523958, 40.521875, 40.519792, 40.517708,
       40.515625, 40.513542, 40.511458, 40.509375, 40.507292, 40.505208,
       40.503125, 40.501042, 40.498958, 40.496875, 40.494792, 40.492708,
       40.490625, 40.488542, 40.486458, 40.484375, 40.482292, 40.480208,
       40.478125, 40.476042, 40.473958, 40.471875, 40.469792, 40.467708,
       40.465625, 40.463542, 40.461458])</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span>spatial_ref</span></div><div class='xr-var-dims'>()</div><div class='xr-var-dtype'>int64</div><div class='xr-var-preview xr-preview'>0</div><input id='attrs-c6828b76-ab51-4c53-a4b9-92d7db97f43e' class='xr-var-attrs-in' type='checkbox' ><label for='attrs-c6828b76-ab51-4c53-a4b9-92d7db97f43e' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-fc66bde5-682c-4147-a521-d4b44d3bba08' class='xr-var-data-in' type='checkbox'><label for='data-fc66bde5-682c-4147-a521-d4b44d3bba08' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'><dt><span>crs_wkt :</span></dt><dd>GEOGCS[&quot;WGS 84&quot;,DATUM[&quot;WGS_1984&quot;,SPHEROID[&quot;WGS 84&quot;,6378137,298.257223563,AUTHORITY[&quot;EPSG&quot;,&quot;7030&quot;]],AUTHORITY[&quot;EPSG&quot;,&quot;6326&quot;]],PRIMEM[&quot;Greenwich&quot;,0,AUTHORITY[&quot;EPSG&quot;,&quot;8901&quot;]],UNIT[&quot;degree&quot;,0.0174532925199433,AUTHORITY[&quot;EPSG&quot;,&quot;9122&quot;]],AXIS[&quot;Latitude&quot;,NORTH],AXIS[&quot;Longitude&quot;,EAST],AUTHORITY[&quot;EPSG&quot;,&quot;4326&quot;]]</dd><dt><span>semi_major_axis :</span></dt><dd>6378137.0</dd><dt><span>semi_minor_axis :</span></dt><dd>6356752.314245179</dd><dt><span>inverse_flattening :</span></dt><dd>298.257223563</dd><dt><span>reference_ellipsoid_name :</span></dt><dd>WGS 84</dd><dt><span>longitude_of_prime_meridian :</span></dt><dd>0.0</dd><dt><span>prime_meridian_name :</span></dt><dd>Greenwich</dd><dt><span>geographic_crs_name :</span></dt><dd>WGS 84</dd><dt><span>horizontal_datum_name :</span></dt><dd>World Geodetic System 1984</dd><dt><span>grid_mapping_name :</span></dt><dd>latitude_longitude</dd><dt><span>spatial_ref :</span></dt><dd>GEOGCS[&quot;WGS 84&quot;,DATUM[&quot;WGS_1984&quot;,SPHEROID[&quot;WGS 84&quot;,6378137,298.257223563,AUTHORITY[&quot;EPSG&quot;,&quot;7030&quot;]],AUTHORITY[&quot;EPSG&quot;,&quot;6326&quot;]],PRIMEM[&quot;Greenwich&quot;,0,AUTHORITY[&quot;EPSG&quot;,&quot;8901&quot;]],UNIT[&quot;degree&quot;,0.0174532925199433,AUTHORITY[&quot;EPSG&quot;,&quot;9122&quot;]],AXIS[&quot;Latitude&quot;,NORTH],AXIS[&quot;Longitude&quot;,EAST],AUTHORITY[&quot;EPSG&quot;,&quot;4326&quot;]]</dd><dt><span>GeoTransform :</span></dt><dd>-105.90416665717922 0.0020833333331466974 0.0 40.779166663013456 0.0 -0.0020833333331466974</dd></dl></div><div class='xr-var-data'><pre>array(0)</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>date</span></div><div class='xr-var-dims'>(date)</div><div class='xr-var-dtype'>datetime64[ns]</div><div class='xr-var-preview xr-preview'>2018-06-26 ... 2023-07-28</div><input id='attrs-937250fa-401a-47eb-9ed4-1d8f542b3ab3' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-937250fa-401a-47eb-9ed4-1d8f542b3ab3' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-0e719441-b7a5-44f7-a486-130a29ac31c0' class='xr-var-data-in' type='checkbox'><label for='data-0e719441-b7a5-44f7-a486-130a29ac31c0' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([&#x27;2018-06-26T00:00:00.000000000&#x27;, &#x27;2018-07-12T00:00:00.000000000&#x27;,
       &#x27;2018-07-28T00:00:00.000000000&#x27;, &#x27;2019-06-26T00:00:00.000000000&#x27;,
       &#x27;2019-07-12T00:00:00.000000000&#x27;, &#x27;2019-07-28T00:00:00.000000000&#x27;,
       &#x27;2020-06-25T00:00:00.000000000&#x27;, &#x27;2020-07-11T00:00:00.000000000&#x27;,
       &#x27;2020-07-27T00:00:00.000000000&#x27;, &#x27;2021-06-26T00:00:00.000000000&#x27;,
       &#x27;2021-07-12T00:00:00.000000000&#x27;, &#x27;2021-07-28T00:00:00.000000000&#x27;,
       &#x27;2022-06-26T00:00:00.000000000&#x27;, &#x27;2022-07-12T00:00:00.000000000&#x27;,
       &#x27;2022-07-28T00:00:00.000000000&#x27;, &#x27;2023-06-26T00:00:00.000000000&#x27;,
       &#x27;2023-07-12T00:00:00.000000000&#x27;, &#x27;2023-07-28T00:00:00.000000000&#x27;],
      dtype=&#x27;datetime64[ns]&#x27;)</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-3b55572b-1c1c-4854-b70a-37f437972109' class='xr-section-summary-in' type='checkbox'  checked><label for='section-3b55572b-1c1c-4854-b70a-37f437972109' class='xr-section-summary' >Data variables: <span>(1)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-var-name'><span>NDVI</span></div><div class='xr-var-dims'>(date, y, x)</div><div class='xr-var-dtype'>float32</div><div class='xr-var-preview xr-preview'>0.5672 0.5711 ... 0.6327 0.6327</div><input id='attrs-2dec7704-2b35-42be-a802-aa1e3c08b4cb' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-2dec7704-2b35-42be-a802-aa1e3c08b4cb' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-e5344da0-ca17-4dc3-92fd-672102fff2cd' class='xr-var-data-in' type='checkbox'><label for='data-e5344da0-ca17-4dc3-92fd-672102fff2cd' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([[[0.5672, 0.5711, 0.6027, ..., 0.3888, 0.3888, 0.381 ],
        [0.5793, 0.6917, 0.6917, ..., 0.3902, 0.3683, 0.381 ],
        [0.6174, 0.6174, 0.6917, ..., 0.3867, 0.3902, 0.4094],
        ...,
        [0.2816, 0.2816, 0.1774, ..., 0.4708, 0.3777, 0.4174],
        [0.2966, 0.2966, 0.2701, ..., 0.3807, 0.4774, 0.4307],
        [0.363 , 0.2543, 0.2701, ..., 0.3878, 0.4797, 0.4797]],

       [[0.5806, 0.5789, 0.5865, ..., 0.3451, 0.3451, 0.3483],
        [0.5633, 0.6341, 0.6341, ..., 0.3777, 0.3484, 0.3483],
        [0.6144, 0.6144, 0.6309, ..., 0.3511, 0.3484, 0.3376],
        ...,
        [0.2836, 0.2836, 0.2292, ..., 0.4965, 0.4373, 0.4082],
        [0.249 , 0.2925, 0.3079, ..., 0.4385, 0.4865, 0.4492],
        [0.4257, 0.2689, 0.2137, ..., 0.4532, 0.4682, 0.4682]],

       [[0.6154, 0.5971, 0.5883, ..., 0.3806, 0.3806, 0.3809],
        [0.6154, 0.6525, 0.6525, ..., 0.4257, 0.363 , 0.3619],
        [0.5944, 0.5944, 0.6709, ..., 0.3582, 0.3541, 0.3617],
        ...,
...
        [0.1624, 0.1624, 0.0984, ..., 0.6586, 0.6443, 0.5771],
        [0.2924, 0.2428, 0.0984, ..., 0.659 , 0.7146, 0.678 ],
        [0.1628, 0.2924, 0.2267, ..., 0.659 , 0.7149, 0.7149]],

       [[0.6071, 0.5808, 0.5888, ..., 0.4637, 0.4637, 0.4901],
        [0.6071, 0.6501, 0.6501, ..., 0.4922, 0.4738, 0.4317],
        [0.6792, 0.6792, 0.6792, ..., 0.4516, 0.4334, 0.4317],
        ...,
        [0.213 , 0.213 , 0.1423, ..., 0.6524, 0.6861, 0.5645],
        [0.3889, 0.3428, 0.0974, ..., 0.6346, 0.686 , 0.6473],
        [0.4555, 0.3806, 0.3308, ..., 0.6316, 0.6616, 0.6616]],

       [[0.5787, 0.6146, 0.6053, ..., 0.427 , 0.427 , 0.427 ],
        [0.6303, 0.6303, 0.6303, ..., 0.4454, 0.4249, 0.4264],
        [0.6415, 0.6415, 0.6334, ..., 0.4316, 0.4117, 0.4594],
        ...,
        [0.24  , 0.24  , 0.2518, ..., 0.6657, 0.6761, 0.5383],
        [0.2111, 0.1931, 0.1973, ..., 0.6778, 0.7129, 0.603 ],
        [0.3925, 0.364 , 0.2705, ..., 0.6235, 0.6327, 0.6327]]],
      dtype=float32)</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-6708156f-e2bf-4e0f-a9ea-789dc3297583' class='xr-section-summary-in' type='checkbox'  ><label for='section-6708156f-e2bf-4e0f-a9ea-789dc3297583' class='xr-section-summary' >Indexes: <span>(3)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-index-name'><div>x</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-f694cbd3-0db4-4c21-b542-502fec849784' class='xr-index-data-in' type='checkbox'/><label for='index-f694cbd3-0db4-4c21-b542-502fec849784' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(Index([-105.90312499051265,  -105.9010416571795, -105.89895832384636,
       -105.89687499051321, -105.89479165718006, -105.89270832384692,
       -105.89062499051377, -105.88854165718062, -105.88645832384748,
       -105.88437499051433,
       ...
       -105.21770832390739, -105.21562499057424,  -105.2135416572411,
       -105.21145832390795,  -105.2093749905748, -105.20729165724165,
       -105.20520832390851, -105.20312499057536, -105.20104165724221,
       -105.19895832390907],
      dtype=&#x27;float64&#x27;, name=&#x27;x&#x27;, length=339))</pre></div></li><li class='xr-var-item'><div class='xr-index-name'><div>y</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-7995a6a9-337f-40a4-a39c-621cc8d12fa9' class='xr-index-data-in' type='checkbox'/><label for='index-7995a6a9-337f-40a4-a39c-621cc8d12fa9' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(Index([ 40.77812499634688, 40.776041663013736,  40.77395832968059,
        40.77187499634744, 40.769791663014296,  40.76770832968115,
          40.765624996348, 40.763541663014855,  40.76145832968171,
        40.75937499634856,
       ...
       40.480208329706905,  40.47812499637376,  40.47604166304061,
       40.473958329707465,  40.47187499637432,  40.46979166304117,
       40.467708329708024,  40.46562499637488,  40.46354166304173,
       40.461458329708584],
      dtype=&#x27;float64&#x27;, name=&#x27;y&#x27;, length=153))</pre></div></li><li class='xr-var-item'><div class='xr-index-name'><div>date</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-ad80442a-40a2-469a-89f1-a214cafd6f1c' class='xr-index-data-in' type='checkbox'/><label for='index-ad80442a-40a2-469a-89f1-a214cafd6f1c' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(DatetimeIndex([&#x27;2018-06-26&#x27;, &#x27;2018-07-12&#x27;, &#x27;2018-07-28&#x27;, &#x27;2019-06-26&#x27;,
               &#x27;2019-07-12&#x27;, &#x27;2019-07-28&#x27;, &#x27;2020-06-25&#x27;, &#x27;2020-07-11&#x27;,
               &#x27;2020-07-27&#x27;, &#x27;2021-06-26&#x27;, &#x27;2021-07-12&#x27;, &#x27;2021-07-28&#x27;,
               &#x27;2022-06-26&#x27;, &#x27;2022-07-12&#x27;, &#x27;2022-07-28&#x27;, &#x27;2023-06-26&#x27;,
               &#x27;2023-07-12&#x27;, &#x27;2023-07-28&#x27;],
              dtype=&#x27;datetime64[ns]&#x27;, name=&#x27;date&#x27;, freq=None))</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-ca3da236-ec1b-402e-b46c-b5504cf48c3f' class='xr-section-summary-in' type='checkbox' disabled ><label for='section-ca3da236-ec1b-402e-b46c-b5504cf48c3f' class='xr-section-summary'  title='Expand/collapse section'>Attributes: <span>(0)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><dl class='xr-attrs'></dl></div></li></ul></div></div>




```python
# Slice the time series for the specified time periods
fig, ax = plt.subplots()

# Ensure 'time' is the correct dimension or coordinate name for time in your DataArray
gvndvi_diff = (
  ndvi_da
     .sel(date=slice('2021', '2023'))  # Use 'time' instead of 'date' if 'time' is the dimension or coordinate name
     .mean('date')  # Calculate the mean along the time dimension
     .NDVI  # Access the NDVI variable or data
 - ndvi_da
     .sel(date=slice('2018', '2020'))  # Use 'time' instead of 'date' if 'time' is the dimension or coordinate name
     .mean('date')  # Calculate the mean along the time dimension
     .NDVI  # Access the NDVI variable or data
)

# Visualize the result
gvndvi_diff.plot(x='x', y='y', cmap='PiYG', ax=ax)
cameron_peak_gdf.plot(edgecolor='black', ax=ax)



```




    <Axes: title={'center': 'band = 1, spatial_ref = 0'}, xlabel='x', ylabel='y'>




    
![png](vegetation_files/vegetation_18_1.png)
    



```python
# Save Fig as Image
fig.savefig('cameron.jpg')
```


```python
outside_gdf = (
    gpd.GeoDataFrame(geometry=cameron_peak_gdf.envelope)
    .overlay(cameron_peak_gdf, how='difference'))
```


```python
# Clip data to both inside and outside the boundary
ndvi_cp_da = ndvi_da.rio.clip(cameron_peak_gdf.geometry, from_disk=True)
ndvi_out_da = ndvi_da.rio.clip(outside_gdf.geometry, from_disk=True)
```


>
> For **both inside and outside** the fire boundary:
>
> -   Group the data by year
> -   Take the mean. You always need to tell reducing methods in
>     `xarray` what dimensions you want to reduce. When you want to
>     summarize data across **all** dimensions, you can use the `...`
>     syntax, e.g. `.mean(...)` as a shorthand.
> -   Select the NDVI variable
> -   Convert to a DataFrame using the `to_dataframe()` method
> -   Join the two DataFrames for plotting using the `.join()` method.
>     You will need to rename the columns using the `lsuffix=` and
>     `rsuffix=` parameters

> **<i class="fa fa-solid fa-exclamation fa-large" aria-label="exclamation"></i>
> GOTCHA ALERT**
>
> The DateIndex in pandas is a little different from the Datetime
> Dimension in xarray. You will need to use the `.dt.year` syntax to
> access information about the year, not just `.year`.

Finally, plot annual July means for both inside and outside the
Reservation on the same plot.

:::


```python
# Compute mean annual July NDVI
jul_ndvi_cp_df = (
    ndvi_cp_da
    .groupby(ndvi_cp_da.date.dt.year)
    .mean(...)
    .NDVI.to_dataframe())
jul_ndvi_out_df = (
    ndvi_out_da
    .groupby(ndvi_out_da.date.dt.year)
    .mean(...)
    .NDVI.to_dataframe())

# Plot inside and outside the reservation
jul_ndvi_df = (
    jul_ndvi_cp_df[['NDVI']]
    .join(
        jul_ndvi_out_df[['NDVI']], 
        lsuffix=' Burned Area', rsuffix=' Unburned Area')
)

jul_ndvi_df.hvplot(
    title='NDVI before and after the Cameron Peak Fire'
)
```






<div id='p1004'>
  <div id="b58bcca7-9d90-4c85-980d-29c01a8efaf6" data-root-id="p1004" style="display: contents;"></div>
</div>
<script type="application/javascript">(function(root) {
  var docs_json = {"1d342497-d8f9-4294-8ac0-58f05db136f1":{"version":"3.3.4","title":"Bokeh Application","roots":[{"type":"object","name":"Row","id":"p1004","attributes":{"name":"Row00971","tags":["embedded"],"stylesheets":["\n:host(.pn-loading.pn-arc):before, .pn-loading.pn-arc:before {\n  background-image: url(\"data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHN0eWxlPSJtYXJnaW46IGF1dG87IGJhY2tncm91bmQ6IG5vbmU7IGRpc3BsYXk6IGJsb2NrOyBzaGFwZS1yZW5kZXJpbmc6IGF1dG87IiB2aWV3Qm94PSIwIDAgMTAwIDEwMCIgcHJlc2VydmVBc3BlY3RSYXRpbz0ieE1pZFlNaWQiPiAgPGNpcmNsZSBjeD0iNTAiIGN5PSI1MCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjYzNjM2MzIiBzdHJva2Utd2lkdGg9IjEwIiByPSIzNSIgc3Ryb2tlLWRhc2hhcnJheT0iMTY0LjkzMzYxNDMxMzQ2NDE1IDU2Ljk3Nzg3MTQzNzgyMTM4Ij4gICAgPGFuaW1hdGVUcmFuc2Zvcm0gYXR0cmlidXRlTmFtZT0idHJhbnNmb3JtIiB0eXBlPSJyb3RhdGUiIHJlcGVhdENvdW50PSJpbmRlZmluaXRlIiBkdXI9IjFzIiB2YWx1ZXM9IjAgNTAgNTA7MzYwIDUwIDUwIiBrZXlUaW1lcz0iMDsxIj48L2FuaW1hdGVUcmFuc2Zvcm0+ICA8L2NpcmNsZT48L3N2Zz4=\");\n  background-size: auto calc(min(50%, 400px));\n}",{"type":"object","name":"ImportedStyleSheet","id":"p1007","attributes":{"url":"https://cdn.holoviz.org/panel/1.3.8/dist/css/loading.css"}},{"type":"object","name":"ImportedStyleSheet","id":"p1082","attributes":{"url":"https://cdn.holoviz.org/panel/1.3.8/dist/css/listpanel.css"}},{"type":"object","name":"ImportedStyleSheet","id":"p1005","attributes":{"url":"https://cdn.holoviz.org/panel/1.3.8/dist/bundled/theme/default.css"}},{"type":"object","name":"ImportedStyleSheet","id":"p1006","attributes":{"url":"https://cdn.holoviz.org/panel/1.3.8/dist/bundled/theme/native.css"}}],"min_width":700,"margin":0,"sizing_mode":"stretch_width","align":"start","children":[{"type":"object","name":"Spacer","id":"p1008","attributes":{"name":"HSpacer00978","stylesheets":["\n:host(.pn-loading.pn-arc):before, .pn-loading.pn-arc:before {\n  background-image: url(\"data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHN0eWxlPSJtYXJnaW46IGF1dG87IGJhY2tncm91bmQ6IG5vbmU7IGRpc3BsYXk6IGJsb2NrOyBzaGFwZS1yZW5kZXJpbmc6IGF1dG87IiB2aWV3Qm94PSIwIDAgMTAwIDEwMCIgcHJlc2VydmVBc3BlY3RSYXRpbz0ieE1pZFlNaWQiPiAgPGNpcmNsZSBjeD0iNTAiIGN5PSI1MCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjYzNjM2MzIiBzdHJva2Utd2lkdGg9IjEwIiByPSIzNSIgc3Ryb2tlLWRhc2hhcnJheT0iMTY0LjkzMzYxNDMxMzQ2NDE1IDU2Ljk3Nzg3MTQzNzgyMTM4Ij4gICAgPGFuaW1hdGVUcmFuc2Zvcm0gYXR0cmlidXRlTmFtZT0idHJhbnNmb3JtIiB0eXBlPSJyb3RhdGUiIHJlcGVhdENvdW50PSJpbmRlZmluaXRlIiBkdXI9IjFzIiB2YWx1ZXM9IjAgNTAgNTA7MzYwIDUwIDUwIiBrZXlUaW1lcz0iMDsxIj48L2FuaW1hdGVUcmFuc2Zvcm0+ICA8L2NpcmNsZT48L3N2Zz4=\");\n  background-size: auto calc(min(50%, 400px));\n}",{"id":"p1007"},{"id":"p1005"},{"id":"p1006"}],"margin":0,"sizing_mode":"stretch_width","align":"start"}},{"type":"object","name":"Figure","id":"p1019","attributes":{"width":700,"height":300,"margin":[5,10],"sizing_mode":"fixed","align":"start","x_range":{"type":"object","name":"Range1d","id":"p1009","attributes":{"tags":[[["year","year",null]],[]],"start":2018.0,"end":2023.0,"reset_start":2018.0,"reset_end":2023.0}},"y_range":{"type":"object","name":"Range1d","id":"p1010","attributes":{"tags":[[["value","value",null]],{"type":"map","entries":[["invert_yaxis",false],["autorange",false]]}],"start":0.3739443182945251,"end":0.6505683302879334,"reset_start":0.3739443182945251,"reset_end":0.6505683302879334}},"x_scale":{"type":"object","name":"LinearScale","id":"p1029"},"y_scale":{"type":"object","name":"LinearScale","id":"p1030"},"title":{"type":"object","name":"Title","id":"p1022","attributes":{"text":"NDVI before and after the Cameron Peak Fire","text_color":"black","text_font_size":"12pt"}},"renderers":[{"type":"object","name":"GlyphRenderer","id":"p1059","attributes":{"name":"NDVI Burned Area","data_source":{"type":"object","name":"ColumnDataSource","id":"p1050","attributes":{"selected":{"type":"object","name":"Selection","id":"p1051","attributes":{"indices":[],"line_indices":[]}},"selection_policy":{"type":"object","name":"UnionRenderers","id":"p1052"},"data":{"type":"map","entries":[["year",{"type":"ndarray","array":{"type":"bytes","data":"4gcAAOMHAADkBwAA5QcAAOYHAADnBwAA"},"shape":[6],"dtype":"int32","order":"little"}],["value",{"type":"ndarray","array":{"type":"bytes","data":"CNEeP+mkID9f2B0/QvLMPhpDyz6Zi+U+"},"shape":[6],"dtype":"float32","order":"little"}],["Variable",["NDVI Burned Area","NDVI Burned Area","NDVI Burned Area","NDVI Burned Area","NDVI Burned Area","NDVI Burned Area"]]]}}},"view":{"type":"object","name":"CDSView","id":"p1060","attributes":{"filter":{"type":"object","name":"AllIndices","id":"p1061"}}},"glyph":{"type":"object","name":"Line","id":"p1056","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"value"},"line_color":"#30a2da","line_width":2}},"selection_glyph":{"type":"object","name":"Line","id":"p1064","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"value"},"line_color":"#30a2da","line_width":2}},"nonselection_glyph":{"type":"object","name":"Line","id":"p1057","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"value"},"line_color":"#30a2da","line_alpha":0.1,"line_width":2}},"muted_glyph":{"type":"object","name":"Line","id":"p1058","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"value"},"line_color":"#30a2da","line_alpha":0.2,"line_width":2}}}},{"type":"object","name":"GlyphRenderer","id":"p1074","attributes":{"name":"NDVI Unburned Area","data_source":{"type":"object","name":"ColumnDataSource","id":"p1065","attributes":{"selected":{"type":"object","name":"Selection","id":"p1066","attributes":{"indices":[],"line_indices":[]}},"selection_policy":{"type":"object","name":"UnionRenderers","id":"p1067"},"data":{"type":"map","entries":[["year",{"type":"ndarray","array":{"type":"bytes","data":"4gcAAOMHAADkBwAA5QcAAOYHAADnBwAA"},"shape":[6],"dtype":"int32","order":"little"}],["value",{"type":"ndarray","array":{"type":"bytes","data":"5mgHP+CaCT+u1AE/iOANP1A//z7ZdQ0/"},"shape":[6],"dtype":"float32","order":"little"}],["Variable",["NDVI Unburned Area","NDVI Unburned Area","NDVI Unburned Area","NDVI Unburned Area","NDVI Unburned Area","NDVI Unburned Area"]]]}}},"view":{"type":"object","name":"CDSView","id":"p1075","attributes":{"filter":{"type":"object","name":"AllIndices","id":"p1076"}}},"glyph":{"type":"object","name":"Line","id":"p1071","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"value"},"line_color":"#fc4f30","line_width":2}},"selection_glyph":{"type":"object","name":"Line","id":"p1078","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"value"},"line_color":"#fc4f30","line_width":2}},"nonselection_glyph":{"type":"object","name":"Line","id":"p1072","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"value"},"line_color":"#fc4f30","line_alpha":0.1,"line_width":2}},"muted_glyph":{"type":"object","name":"Line","id":"p1073","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"value"},"line_color":"#fc4f30","line_alpha":0.2,"line_width":2}}}}],"toolbar":{"type":"object","name":"Toolbar","id":"p1028","attributes":{"tools":[{"type":"object","name":"WheelZoomTool","id":"p1014","attributes":{"tags":["hv_created"],"renderers":"auto","zoom_together":"none"}},{"type":"object","name":"HoverTool","id":"p1015","attributes":{"tags":["hv_created"],"renderers":[{"id":"p1059"},{"id":"p1074"}],"tooltips":[["Variable","@{Variable}"],["year","@{year}"],["value","@{value}"]]}},{"type":"object","name":"SaveTool","id":"p1041"},{"type":"object","name":"PanTool","id":"p1042"},{"type":"object","name":"BoxZoomTool","id":"p1043","attributes":{"overlay":{"type":"object","name":"BoxAnnotation","id":"p1044","attributes":{"syncable":false,"level":"overlay","visible":false,"left":{"type":"number","value":"nan"},"right":{"type":"number","value":"nan"},"top":{"type":"number","value":"nan"},"bottom":{"type":"number","value":"nan"},"left_units":"canvas","right_units":"canvas","top_units":"canvas","bottom_units":"canvas","line_color":"black","line_alpha":1.0,"line_width":2,"line_dash":[4,4],"fill_color":"lightgrey","fill_alpha":0.5}}}},{"type":"object","name":"ResetTool","id":"p1049"}],"active_drag":{"id":"p1042"},"active_scroll":{"id":"p1014"}}},"left":[{"type":"object","name":"LinearAxis","id":"p1036","attributes":{"ticker":{"type":"object","name":"BasicTicker","id":"p1037","attributes":{"mantissas":[1,2,5]}},"formatter":{"type":"object","name":"BasicTickFormatter","id":"p1038"},"axis_label":"","major_label_policy":{"type":"object","name":"AllLabels","id":"p1039"}}}],"right":[{"type":"object","name":"Legend","id":"p1062","attributes":{"location":[0,0],"title":"Variable","click_policy":"mute","items":[{"type":"object","name":"LegendItem","id":"p1063","attributes":{"label":{"type":"value","value":"NDVI Burned Area"},"renderers":[{"id":"p1059"}]}},{"type":"object","name":"LegendItem","id":"p1077","attributes":{"label":{"type":"value","value":"NDVI Unburned Area"},"renderers":[{"id":"p1074"}]}}]}}],"below":[{"type":"object","name":"LinearAxis","id":"p1031","attributes":{"ticker":{"type":"object","name":"BasicTicker","id":"p1032","attributes":{"mantissas":[1,2,5]}},"formatter":{"type":"object","name":"BasicTickFormatter","id":"p1033"},"axis_label":"year","major_label_policy":{"type":"object","name":"AllLabels","id":"p1034"}}}],"center":[{"type":"object","name":"Grid","id":"p1035","attributes":{"axis":{"id":"p1031"},"grid_line_color":null}},{"type":"object","name":"Grid","id":"p1040","attributes":{"dimension":1,"axis":{"id":"p1036"},"grid_line_color":null}}],"min_border_top":10,"min_border_bottom":10,"min_border_left":10,"min_border_right":10,"output_backend":"webgl"}},{"type":"object","name":"Spacer","id":"p1080","attributes":{"name":"HSpacer00979","stylesheets":["\n:host(.pn-loading.pn-arc):before, .pn-loading.pn-arc:before {\n  background-image: url(\"data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHN0eWxlPSJtYXJnaW46IGF1dG87IGJhY2tncm91bmQ6IG5vbmU7IGRpc3BsYXk6IGJsb2NrOyBzaGFwZS1yZW5kZXJpbmc6IGF1dG87IiB2aWV3Qm94PSIwIDAgMTAwIDEwMCIgcHJlc2VydmVBc3BlY3RSYXRpbz0ieE1pZFlNaWQiPiAgPGNpcmNsZSBjeD0iNTAiIGN5PSI1MCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjYzNjM2MzIiBzdHJva2Utd2lkdGg9IjEwIiByPSIzNSIgc3Ryb2tlLWRhc2hhcnJheT0iMTY0LjkzMzYxNDMxMzQ2NDE1IDU2Ljk3Nzg3MTQzNzgyMTM4Ij4gICAgPGFuaW1hdGVUcmFuc2Zvcm0gYXR0cmlidXRlTmFtZT0idHJhbnNmb3JtIiB0eXBlPSJyb3RhdGUiIHJlcGVhdENvdW50PSJpbmRlZmluaXRlIiBkdXI9IjFzIiB2YWx1ZXM9IjAgNTAgNTA7MzYwIDUwIDUwIiBrZXlUaW1lcz0iMDsxIj48L2FuaW1hdGVUcmFuc2Zvcm0+ICA8L2NpcmNsZT48L3N2Zz4=\");\n  background-size: auto calc(min(50%, 400px));\n}",{"id":"p1007"},{"id":"p1005"},{"id":"p1006"}],"margin":0,"sizing_mode":"stretch_width","align":"start"}}]}}],"defs":[{"type":"model","name":"ReactiveHTML1"},{"type":"model","name":"FlexBox1","properties":[{"name":"align_content","kind":"Any","default":"flex-start"},{"name":"align_items","kind":"Any","default":"flex-start"},{"name":"flex_direction","kind":"Any","default":"row"},{"name":"flex_wrap","kind":"Any","default":"wrap"},{"name":"justify_content","kind":"Any","default":"flex-start"}]},{"type":"model","name":"FloatPanel1","properties":[{"name":"config","kind":"Any","default":{"type":"map"}},{"name":"contained","kind":"Any","default":true},{"name":"position","kind":"Any","default":"right-top"},{"name":"offsetx","kind":"Any","default":null},{"name":"offsety","kind":"Any","default":null},{"name":"theme","kind":"Any","default":"primary"},{"name":"status","kind":"Any","default":"normalized"}]},{"type":"model","name":"GridStack1","properties":[{"name":"mode","kind":"Any","default":"warn"},{"name":"ncols","kind":"Any","default":null},{"name":"nrows","kind":"Any","default":null},{"name":"allow_resize","kind":"Any","default":true},{"name":"allow_drag","kind":"Any","default":true},{"name":"state","kind":"Any","default":[]}]},{"type":"model","name":"drag1","properties":[{"name":"slider_width","kind":"Any","default":5},{"name":"slider_color","kind":"Any","default":"black"},{"name":"value","kind":"Any","default":50}]},{"type":"model","name":"click1","properties":[{"name":"terminal_output","kind":"Any","default":""},{"name":"debug_name","kind":"Any","default":""},{"name":"clears","kind":"Any","default":0}]},{"type":"model","name":"copy_to_clipboard1","properties":[{"name":"fill","kind":"Any","default":"none"},{"name":"value","kind":"Any","default":null}]},{"type":"model","name":"FastWrapper1","properties":[{"name":"object","kind":"Any","default":null},{"name":"style","kind":"Any","default":null}]},{"type":"model","name":"NotificationAreaBase1","properties":[{"name":"js_events","kind":"Any","default":{"type":"map"}},{"name":"position","kind":"Any","default":"bottom-right"},{"name":"_clear","kind":"Any","default":0}]},{"type":"model","name":"NotificationArea1","properties":[{"name":"js_events","kind":"Any","default":{"type":"map"}},{"name":"notifications","kind":"Any","default":[]},{"name":"position","kind":"Any","default":"bottom-right"},{"name":"_clear","kind":"Any","default":0},{"name":"types","kind":"Any","default":[{"type":"map","entries":[["type","warning"],["background","#ffc107"],["icon",{"type":"map","entries":[["className","fas fa-exclamation-triangle"],["tagName","i"],["color","white"]]}]]},{"type":"map","entries":[["type","info"],["background","#007bff"],["icon",{"type":"map","entries":[["className","fas fa-info-circle"],["tagName","i"],["color","white"]]}]]}]}]},{"type":"model","name":"Notification","properties":[{"name":"background","kind":"Any","default":null},{"name":"duration","kind":"Any","default":3000},{"name":"icon","kind":"Any","default":null},{"name":"message","kind":"Any","default":""},{"name":"notification_type","kind":"Any","default":null},{"name":"_destroyed","kind":"Any","default":false}]},{"type":"model","name":"TemplateActions1","properties":[{"name":"open_modal","kind":"Any","default":0},{"name":"close_modal","kind":"Any","default":0}]},{"type":"model","name":"BootstrapTemplateActions1","properties":[{"name":"open_modal","kind":"Any","default":0},{"name":"close_modal","kind":"Any","default":0}]},{"type":"model","name":"MaterialTemplateActions1","properties":[{"name":"open_modal","kind":"Any","default":0},{"name":"close_modal","kind":"Any","default":0}]}]}};
  var render_items = [{"docid":"1d342497-d8f9-4294-8ac0-58f05db136f1","roots":{"p1004":"b58bcca7-9d90-4c85-980d-29c01a8efaf6"},"root_ids":["p1004"]}];
  var docs = Object.values(docs_json)
  if (!docs) {
    return
  }
  const py_version = docs[0].version.replace('rc', '-rc.').replace('.dev', '-dev.')
  function embed_document(root) {
    var Bokeh = get_bokeh(root)
    Bokeh.embed.embed_items_notebook(docs_json, render_items);
    for (const render_item of render_items) {
      for (const root_id of render_item.root_ids) {
	const id_el = document.getElementById(root_id)
	if (id_el.children.length && (id_el.children[0].className === 'bk-root')) {
	  const root_el = id_el.children[0]
	  root_el.id = root_el.id + '-rendered'
	}
      }
    }
  }
  function get_bokeh(root) {
    if (root.Bokeh === undefined) {
      return null
    } else if (root.Bokeh.version !== py_version) {
      if (root.Bokeh.versions === undefined || !root.Bokeh.versions.has(py_version)) {
	return null
      }
      return root.Bokeh.versions.get(py_version);
    } else if (root.Bokeh.version === py_version) {
      return root.Bokeh
    }
    return null
  }
  function is_loaded(root) {
    var Bokeh = get_bokeh(root)
    return (Bokeh != null && Bokeh.Panel !== undefined)
  }
  if (is_loaded(root)) {
    embed_document(root);
  } else {
    var attempts = 0;
    var timer = setInterval(function(root) {
      if (is_loaded(root)) {
        clearInterval(timer);
        embed_document(root);
      } else if (document.readyState == "complete") {
        attempts++;
        if (attempts > 200) {
          clearInterval(timer);
	  var Bokeh = get_bokeh(root)
	  if (Bokeh == null || Bokeh.Panel == null) {
            console.warn("Panel: ERROR: Unable to run Panel code because Bokeh or Panel library is missing");
	  } else {
	    console.warn("Panel: WARNING: Attempting to render but not all required libraries could be resolved.")
	    embed_document(root)
	  }
        }
      }
    }, 25, root)
  }
})(window);</script>






```python
# Plot difference inside and outside the reservation
jul_ndvi_df['difference'] = (
    jul_ndvi_df['NDVI Burned Area']
    - jul_ndvi_df['NDVI Unburned Area'])
jul_ndvi_df.difference.hvplot(
    title='Difference between NDVI within and outside the Cameron Peak Fire'
)
```






<div id='p1088'>
  <div id="b0b1de56-193e-4d4b-abc2-7b3915048edf" data-root-id="p1088" style="display: contents;"></div>
</div>
<script type="application/javascript">(function(root) {
  var docs_json = {"5810d1d2-d3dc-4d70-9c82-dfb46ab86dce":{"version":"3.3.4","title":"Bokeh Application","roots":[{"type":"object","name":"Row","id":"p1088","attributes":{"name":"Row01102","tags":["embedded"],"stylesheets":["\n:host(.pn-loading.pn-arc):before, .pn-loading.pn-arc:before {\n  background-image: url(\"data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHN0eWxlPSJtYXJnaW46IGF1dG87IGJhY2tncm91bmQ6IG5vbmU7IGRpc3BsYXk6IGJsb2NrOyBzaGFwZS1yZW5kZXJpbmc6IGF1dG87IiB2aWV3Qm94PSIwIDAgMTAwIDEwMCIgcHJlc2VydmVBc3BlY3RSYXRpbz0ieE1pZFlNaWQiPiAgPGNpcmNsZSBjeD0iNTAiIGN5PSI1MCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjYzNjM2MzIiBzdHJva2Utd2lkdGg9IjEwIiByPSIzNSIgc3Ryb2tlLWRhc2hhcnJheT0iMTY0LjkzMzYxNDMxMzQ2NDE1IDU2Ljk3Nzg3MTQzNzgyMTM4Ij4gICAgPGFuaW1hdGVUcmFuc2Zvcm0gYXR0cmlidXRlTmFtZT0idHJhbnNmb3JtIiB0eXBlPSJyb3RhdGUiIHJlcGVhdENvdW50PSJpbmRlZmluaXRlIiBkdXI9IjFzIiB2YWx1ZXM9IjAgNTAgNTA7MzYwIDUwIDUwIiBrZXlUaW1lcz0iMDsxIj48L2FuaW1hdGVUcmFuc2Zvcm0+ICA8L2NpcmNsZT48L3N2Zz4=\");\n  background-size: auto calc(min(50%, 400px));\n}",{"type":"object","name":"ImportedStyleSheet","id":"p1091","attributes":{"url":"https://cdn.holoviz.org/panel/1.3.8/dist/css/loading.css"}},{"type":"object","name":"ImportedStyleSheet","id":"p1147","attributes":{"url":"https://cdn.holoviz.org/panel/1.3.8/dist/css/listpanel.css"}},{"type":"object","name":"ImportedStyleSheet","id":"p1089","attributes":{"url":"https://cdn.holoviz.org/panel/1.3.8/dist/bundled/theme/default.css"}},{"type":"object","name":"ImportedStyleSheet","id":"p1090","attributes":{"url":"https://cdn.holoviz.org/panel/1.3.8/dist/bundled/theme/native.css"}}],"min_width":700,"margin":0,"sizing_mode":"stretch_width","align":"start","children":[{"type":"object","name":"Spacer","id":"p1092","attributes":{"name":"HSpacer01109","stylesheets":["\n:host(.pn-loading.pn-arc):before, .pn-loading.pn-arc:before {\n  background-image: url(\"data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHN0eWxlPSJtYXJnaW46IGF1dG87IGJhY2tncm91bmQ6IG5vbmU7IGRpc3BsYXk6IGJsb2NrOyBzaGFwZS1yZW5kZXJpbmc6IGF1dG87IiB2aWV3Qm94PSIwIDAgMTAwIDEwMCIgcHJlc2VydmVBc3BlY3RSYXRpbz0ieE1pZFlNaWQiPiAgPGNpcmNsZSBjeD0iNTAiIGN5PSI1MCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjYzNjM2MzIiBzdHJva2Utd2lkdGg9IjEwIiByPSIzNSIgc3Ryb2tlLWRhc2hhcnJheT0iMTY0LjkzMzYxNDMxMzQ2NDE1IDU2Ljk3Nzg3MTQzNzgyMTM4Ij4gICAgPGFuaW1hdGVUcmFuc2Zvcm0gYXR0cmlidXRlTmFtZT0idHJhbnNmb3JtIiB0eXBlPSJyb3RhdGUiIHJlcGVhdENvdW50PSJpbmRlZmluaXRlIiBkdXI9IjFzIiB2YWx1ZXM9IjAgNTAgNTA7MzYwIDUwIDUwIiBrZXlUaW1lcz0iMDsxIj48L2FuaW1hdGVUcmFuc2Zvcm0+ICA8L2NpcmNsZT48L3N2Zz4=\");\n  background-size: auto calc(min(50%, 400px));\n}",{"id":"p1091"},{"id":"p1089"},{"id":"p1090"}],"margin":0,"sizing_mode":"stretch_width","align":"start"}},{"type":"object","name":"Figure","id":"p1100","attributes":{"width":700,"height":300,"margin":[5,10],"sizing_mode":"fixed","align":"start","x_range":{"type":"object","name":"Range1d","id":"p1093","attributes":{"tags":[[["year","year",null]],[]],"start":2018.0,"end":2023.0,"reset_start":2018.0,"reset_end":2023.0}},"y_range":{"type":"object","name":"Range1d","id":"p1094","attributes":{"tags":[[["difference","difference",null]],{"type":"map","entries":[["invert_yaxis",false],["autorange",false]]}],"start":-0.18025683164596557,"end":0.13576661348342894,"reset_start":-0.18025683164596557,"reset_end":0.13576661348342894}},"x_scale":{"type":"object","name":"LinearScale","id":"p1110"},"y_scale":{"type":"object","name":"LinearScale","id":"p1111"},"title":{"type":"object","name":"Title","id":"p1103","attributes":{"text":"Difference between NDVI within and outside the Cameron Peak Fire","text_color":"black","text_font_size":"12pt"}},"renderers":[{"type":"object","name":"GlyphRenderer","id":"p1140","attributes":{"data_source":{"type":"object","name":"ColumnDataSource","id":"p1131","attributes":{"selected":{"type":"object","name":"Selection","id":"p1132","attributes":{"indices":[],"line_indices":[]}},"selection_policy":{"type":"object","name":"UnionRenderers","id":"p1133"},"data":{"type":"map","entries":[["year",{"type":"ndarray","array":{"type":"bytes","data":"4gcAAOMHAADkBwAA5QcAAOYHAADnBwAA"},"shape":[6],"dtype":"int32","order":"little"}],["difference",{"type":"ndarray","array":{"type":"bytes","data":"EEG7PUhQuD2IHeA9nJ0dvtjwz71kgNW9"},"shape":[6],"dtype":"float32","order":"little"}]]}}},"view":{"type":"object","name":"CDSView","id":"p1141","attributes":{"filter":{"type":"object","name":"AllIndices","id":"p1142"}}},"glyph":{"type":"object","name":"Line","id":"p1137","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"difference"},"line_color":"#30a2da","line_width":2}},"selection_glyph":{"type":"object","name":"Line","id":"p1143","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"difference"},"line_color":"#30a2da","line_width":2}},"nonselection_glyph":{"type":"object","name":"Line","id":"p1138","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"difference"},"line_color":"#30a2da","line_alpha":0.1,"line_width":2}},"muted_glyph":{"type":"object","name":"Line","id":"p1139","attributes":{"tags":["apply_ranges"],"x":{"type":"field","field":"year"},"y":{"type":"field","field":"difference"},"line_color":"#30a2da","line_alpha":0.2,"line_width":2}}}}],"toolbar":{"type":"object","name":"Toolbar","id":"p1109","attributes":{"tools":[{"type":"object","name":"WheelZoomTool","id":"p1098","attributes":{"tags":["hv_created"],"renderers":"auto","zoom_together":"none"}},{"type":"object","name":"HoverTool","id":"p1099","attributes":{"tags":["hv_created"],"renderers":[{"id":"p1140"}],"tooltips":[["year","@{year}"],["difference","@{difference}"]]}},{"type":"object","name":"SaveTool","id":"p1122"},{"type":"object","name":"PanTool","id":"p1123"},{"type":"object","name":"BoxZoomTool","id":"p1124","attributes":{"overlay":{"type":"object","name":"BoxAnnotation","id":"p1125","attributes":{"syncable":false,"level":"overlay","visible":false,"left":{"type":"number","value":"nan"},"right":{"type":"number","value":"nan"},"top":{"type":"number","value":"nan"},"bottom":{"type":"number","value":"nan"},"left_units":"canvas","right_units":"canvas","top_units":"canvas","bottom_units":"canvas","line_color":"black","line_alpha":1.0,"line_width":2,"line_dash":[4,4],"fill_color":"lightgrey","fill_alpha":0.5}}}},{"type":"object","name":"ResetTool","id":"p1130"}],"active_drag":{"id":"p1123"},"active_scroll":{"id":"p1098"}}},"left":[{"type":"object","name":"LinearAxis","id":"p1117","attributes":{"ticker":{"type":"object","name":"BasicTicker","id":"p1118","attributes":{"mantissas":[1,2,5]}},"formatter":{"type":"object","name":"BasicTickFormatter","id":"p1119"},"axis_label":"","major_label_policy":{"type":"object","name":"AllLabels","id":"p1120"}}}],"below":[{"type":"object","name":"LinearAxis","id":"p1112","attributes":{"ticker":{"type":"object","name":"BasicTicker","id":"p1113","attributes":{"mantissas":[1,2,5]}},"formatter":{"type":"object","name":"BasicTickFormatter","id":"p1114"},"axis_label":"year","major_label_policy":{"type":"object","name":"AllLabels","id":"p1115"}}}],"center":[{"type":"object","name":"Grid","id":"p1116","attributes":{"axis":{"id":"p1112"},"grid_line_color":null}},{"type":"object","name":"Grid","id":"p1121","attributes":{"dimension":1,"axis":{"id":"p1117"},"grid_line_color":null}}],"min_border_top":10,"min_border_bottom":10,"min_border_left":10,"min_border_right":10,"output_backend":"webgl"}},{"type":"object","name":"Spacer","id":"p1145","attributes":{"name":"HSpacer01110","stylesheets":["\n:host(.pn-loading.pn-arc):before, .pn-loading.pn-arc:before {\n  background-image: url(\"data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHN0eWxlPSJtYXJnaW46IGF1dG87IGJhY2tncm91bmQ6IG5vbmU7IGRpc3BsYXk6IGJsb2NrOyBzaGFwZS1yZW5kZXJpbmc6IGF1dG87IiB2aWV3Qm94PSIwIDAgMTAwIDEwMCIgcHJlc2VydmVBc3BlY3RSYXRpbz0ieE1pZFlNaWQiPiAgPGNpcmNsZSBjeD0iNTAiIGN5PSI1MCIgZmlsbD0ibm9uZSIgc3Ryb2tlPSIjYzNjM2MzIiBzdHJva2Utd2lkdGg9IjEwIiByPSIzNSIgc3Ryb2tlLWRhc2hhcnJheT0iMTY0LjkzMzYxNDMxMzQ2NDE1IDU2Ljk3Nzg3MTQzNzgyMTM4Ij4gICAgPGFuaW1hdGVUcmFuc2Zvcm0gYXR0cmlidXRlTmFtZT0idHJhbnNmb3JtIiB0eXBlPSJyb3RhdGUiIHJlcGVhdENvdW50PSJpbmRlZmluaXRlIiBkdXI9IjFzIiB2YWx1ZXM9IjAgNTAgNTA7MzYwIDUwIDUwIiBrZXlUaW1lcz0iMDsxIj48L2FuaW1hdGVUcmFuc2Zvcm0+ICA8L2NpcmNsZT48L3N2Zz4=\");\n  background-size: auto calc(min(50%, 400px));\n}",{"id":"p1091"},{"id":"p1089"},{"id":"p1090"}],"margin":0,"sizing_mode":"stretch_width","align":"start"}}]}}],"defs":[{"type":"model","name":"ReactiveHTML1"},{"type":"model","name":"FlexBox1","properties":[{"name":"align_content","kind":"Any","default":"flex-start"},{"name":"align_items","kind":"Any","default":"flex-start"},{"name":"flex_direction","kind":"Any","default":"row"},{"name":"flex_wrap","kind":"Any","default":"wrap"},{"name":"justify_content","kind":"Any","default":"flex-start"}]},{"type":"model","name":"FloatPanel1","properties":[{"name":"config","kind":"Any","default":{"type":"map"}},{"name":"contained","kind":"Any","default":true},{"name":"position","kind":"Any","default":"right-top"},{"name":"offsetx","kind":"Any","default":null},{"name":"offsety","kind":"Any","default":null},{"name":"theme","kind":"Any","default":"primary"},{"name":"status","kind":"Any","default":"normalized"}]},{"type":"model","name":"GridStack1","properties":[{"name":"mode","kind":"Any","default":"warn"},{"name":"ncols","kind":"Any","default":null},{"name":"nrows","kind":"Any","default":null},{"name":"allow_resize","kind":"Any","default":true},{"name":"allow_drag","kind":"Any","default":true},{"name":"state","kind":"Any","default":[]}]},{"type":"model","name":"drag1","properties":[{"name":"slider_width","kind":"Any","default":5},{"name":"slider_color","kind":"Any","default":"black"},{"name":"value","kind":"Any","default":50}]},{"type":"model","name":"click1","properties":[{"name":"terminal_output","kind":"Any","default":""},{"name":"debug_name","kind":"Any","default":""},{"name":"clears","kind":"Any","default":0}]},{"type":"model","name":"copy_to_clipboard1","properties":[{"name":"fill","kind":"Any","default":"none"},{"name":"value","kind":"Any","default":null}]},{"type":"model","name":"FastWrapper1","properties":[{"name":"object","kind":"Any","default":null},{"name":"style","kind":"Any","default":null}]},{"type":"model","name":"NotificationAreaBase1","properties":[{"name":"js_events","kind":"Any","default":{"type":"map"}},{"name":"position","kind":"Any","default":"bottom-right"},{"name":"_clear","kind":"Any","default":0}]},{"type":"model","name":"NotificationArea1","properties":[{"name":"js_events","kind":"Any","default":{"type":"map"}},{"name":"notifications","kind":"Any","default":[]},{"name":"position","kind":"Any","default":"bottom-right"},{"name":"_clear","kind":"Any","default":0},{"name":"types","kind":"Any","default":[{"type":"map","entries":[["type","warning"],["background","#ffc107"],["icon",{"type":"map","entries":[["className","fas fa-exclamation-triangle"],["tagName","i"],["color","white"]]}]]},{"type":"map","entries":[["type","info"],["background","#007bff"],["icon",{"type":"map","entries":[["className","fas fa-info-circle"],["tagName","i"],["color","white"]]}]]}]}]},{"type":"model","name":"Notification","properties":[{"name":"background","kind":"Any","default":null},{"name":"duration","kind":"Any","default":3000},{"name":"icon","kind":"Any","default":null},{"name":"message","kind":"Any","default":""},{"name":"notification_type","kind":"Any","default":null},{"name":"_destroyed","kind":"Any","default":false}]},{"type":"model","name":"TemplateActions1","properties":[{"name":"open_modal","kind":"Any","default":0},{"name":"close_modal","kind":"Any","default":0}]},{"type":"model","name":"BootstrapTemplateActions1","properties":[{"name":"open_modal","kind":"Any","default":0},{"name":"close_modal","kind":"Any","default":0}]},{"type":"model","name":"MaterialTemplateActions1","properties":[{"name":"open_modal","kind":"Any","default":0},{"name":"close_modal","kind":"Any","default":0}]}]}};
  var render_items = [{"docid":"5810d1d2-d3dc-4d70-9c82-dfb46ab86dce","roots":{"p1088":"b0b1de56-193e-4d4b-abc2-7b3915048edf"},"root_ids":["p1088"]}];
  var docs = Object.values(docs_json)
  if (!docs) {
    return
  }
  const py_version = docs[0].version.replace('rc', '-rc.').replace('.dev', '-dev.')
  function embed_document(root) {
    var Bokeh = get_bokeh(root)
    Bokeh.embed.embed_items_notebook(docs_json, render_items);
    for (const render_item of render_items) {
      for (const root_id of render_item.root_ids) {
	const id_el = document.getElementById(root_id)
	if (id_el.children.length && (id_el.children[0].className === 'bk-root')) {
	  const root_el = id_el.children[0]
	  root_el.id = root_el.id + '-rendered'
	}
      }
    }
  }
  function get_bokeh(root) {
    if (root.Bokeh === undefined) {
      return null
    } else if (root.Bokeh.version !== py_version) {
      if (root.Bokeh.versions === undefined || !root.Bokeh.versions.has(py_version)) {
	return null
      }
      return root.Bokeh.versions.get(py_version);
    } else if (root.Bokeh.version === py_version) {
      return root.Bokeh
    }
    return null
  }
  function is_loaded(root) {
    var Bokeh = get_bokeh(root)
    return (Bokeh != null && Bokeh.Panel !== undefined)
  }
  if (is_loaded(root)) {
    embed_document(root);
  } else {
    var attempts = 0;
    var timer = setInterval(function(root) {
      if (is_loaded(root)) {
        clearInterval(timer);
        embed_document(root);
      } else if (document.readyState == "complete") {
        attempts++;
        if (attempts > 200) {
          clearInterval(timer);
	  var Bokeh = get_bokeh(root)
	  if (Bokeh == null || Bokeh.Panel == null) {
            console.warn("Panel: ERROR: Unable to run Panel code because Bokeh or Panel library is missing");
	  } else {
	    console.warn("Panel: WARNING: Attempting to render but not all required libraries could be resolved.")
	    embed_document(root)
	  }
        }
      }
    }, 25, root)
  }
})(window);</script>



Observations from the Plot:

From the plot ;

1. In Year 2020 there was a sharp decrease in NDVI values is evident within the burned area,which shows a significant reduction in vegetation due to fire. This decline is visually striking, illustrating the immediate impact of the fire on vegetation health.

2. After 2020, The NDVI values within the burned area begin to get back to normal, although it did not fully reach the pre-fire levels of 2023. This gradual increase shows some degree of vegetative regrowth within the burned region following the fire event.




##  Repeating the workflow in Abuja, Nigeria.




```python
import getpass
import json
import os
import pathlib
from glob import glob
import matplotlib.pyplot as plt

# Library to work with tabular data
import pandas as pd

# Library to work with vector data
import geopandas as gpd

import earthpy.appeears as eaapp
import hvplot.pandas
import hvplot.xarray
import rioxarray as rxr
import xarray as xr
```


```python
abuja_dir = os.path.join(pathlib.Path.home(), 'abuja-data')
# Make the data directory
os.makedirs(abuja_dir, exist_ok=True)

abuja_dir
```




    '/home/jovyan/abuja-data'




```python
# Define url to Nigeria .shp
nigeria_url = "https://open.africa/dataset/83582021-d8f7-4bfd-928f-e9c6c8cb1247/resource/372a616a-66cc-41f7-ac91-d8af8f23bc2b/download/nigeria-lgas.zip"

# Open Nigeria .shp using geopandas
nigeria_gdf = gpd.read_file(nigeria_url)
nigeria_gdf
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>STATE</th>
      <th>LGA</th>
      <th>AREA</th>
      <th>PERIMETER</th>
      <th>LONGITUDE</th>
      <th>LATITUDE</th>
      <th>FULL_NAME</th>
      <th>geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sokoto</td>
      <td>Gada</td>
      <td>1193.977</td>
      <td>170.095</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((5.53632 13.88793, 5.53480 13.88488, ...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Sokoto</td>
      <td>Illela</td>
      <td>1298.423</td>
      <td>174.726</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((5.53632 13.88793, 5.54517 13.88419, ...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Sokoto</td>
      <td>Tangaza</td>
      <td>2460.715</td>
      <td>209.702</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((4.85548 13.76724, 4.86189 13.78085, ...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Borno</td>
      <td>Abadam</td>
      <td>2430.515</td>
      <td>288.957</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((12.83189 13.39871, 12.83397 13.40439...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Lake</td>
      <td>Lake chad</td>
      <td>5225.912</td>
      <td>497.039</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((13.48608 13.30821, 13.48296 13.31344...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>770</th>
      <td>Delta</td>
      <td>Isoko North</td>
      <td>485.467</td>
      <td>169.369</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>MULTIPOLYGON (((6.31996 5.63341, 6.32003 5.633...</td>
    </tr>
    <tr>
      <th>771</th>
      <td>Niger</td>
      <td>Lavun</td>
      <td>3951.431</td>
      <td>424.153</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>MULTIPOLYGON (((6.12188 9.09441, 6.12001 9.094...</td>
    </tr>
    <tr>
      <th>772</th>
      <td>Yobe</td>
      <td>Bade</td>
      <td>817.260</td>
      <td>216.207</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>MULTIPOLYGON (((11.01052 12.80457, 11.00747 12...</td>
    </tr>
    <tr>
      <th>773</th>
      <td>Zamfara</td>
      <td>Maru</td>
      <td>7795.261</td>
      <td>536.500</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>MULTIPOLYGON (((6.43894 12.41104, 6.43609 12.4...</td>
    </tr>
    <tr>
      <th>774</th>
      <td>Akwa Ibom</td>
      <td>Oron</td>
      <td>81.472</td>
      <td>57.846</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((8.22063 4.84473, 8.23405 4.82974, 8....</td>
    </tr>
  </tbody>
</table>
<p>775 rows × 8 columns</p>
</div>




```python
print(nigeria_gdf.columns)
```

    Index(['STATE', 'LGA', 'AREA', 'PERIMETER', 'LONGITUDE', 'LATITUDE',
           'FULL_NAME', 'geometry'],
          dtype='object')



```python
# SEARCH FOR STATE IN DATA FRAME.
search_state = 'Abuja'  # Note: Use capital letters if the column is in capital letters

# Check if the state exists in the specified column
if search_state in nigeria_gdf['STATE'].values:
    print(f"{search_state} exists in the GeoDataFrame.")
else:
    print(f"{search_state} does not exist in the GeoDataFrame.")
```

    Abuja exists in the GeoDataFrame.



```python
# Select out Abuja
Abuja = nigeria_gdf[nigeria_gdf["STATE"]=="Abuja"]
Abuja
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>STATE</th>
      <th>LGA</th>
      <th>AREA</th>
      <th>PERIMETER</th>
      <th>LONGITUDE</th>
      <th>LATITUDE</th>
      <th>FULL_NAME</th>
      <th>geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>300</th>
      <td>Abuja</td>
      <td>Bwari</td>
      <td>818.909</td>
      <td>153.605</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((7.62143 9.30766, 7.62241 9.30840, 7....</td>
    </tr>
    <tr>
      <th>307</th>
      <td>Abuja</td>
      <td>Gwagwalada</td>
      <td>967.762</td>
      <td>129.429</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((7.12020 9.12689, 7.12679 9.11870, 7....</td>
    </tr>
    <tr>
      <th>308</th>
      <td>Abuja</td>
      <td>Abaji</td>
      <td>1002.453</td>
      <td>257.578</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((6.96319 9.23240, 6.96163 9.23123, 6....</td>
    </tr>
    <tr>
      <th>314</th>
      <td>Abuja</td>
      <td>Abuja Municipal</td>
      <td>1585.320</td>
      <td>206.501</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((7.17027 9.09925, 7.17036 9.09829, 7....</td>
    </tr>
    <tr>
      <th>329</th>
      <td>Abuja</td>
      <td>Kwali</td>
      <td>1299.111</td>
      <td>151.853</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((7.05974 8.89200, 7.06064 8.89094, 7....</td>
    </tr>
    <tr>
      <th>331</th>
      <td>Abuja</td>
      <td>Kuje</td>
      <td>1708.466</td>
      <td>217.281</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((7.07285 8.90097, 7.08316 8.90024, 7....</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Display Interactive map of state, In this case (Abuja)
Abuja.explore()
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe srcdoc="&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;

    &lt;meta http-equiv=&quot;content-type&quot; content=&quot;text/html; charset=UTF-8&quot; /&gt;

        &lt;script&gt;
            L_NO_TOUCH = false;
            L_DISABLE_3D = false;
        &lt;/script&gt;

    &lt;style&gt;html, body {width: 100%;height: 100%;margin: 0;padding: 0;}&lt;/style&gt;
    &lt;style&gt;#map {position:absolute;top:0;bottom:0;right:0;left:0;}&lt;/style&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://code.jquery.com/jquery-1.12.4.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/js/bootstrap.bundle.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.2.0/css/all.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/python-visualization/folium/folium/templates/leaflet.awesome.rotate.min.css&quot;/&gt;

            &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width,
                initial-scale=1.0, maximum-scale=1.0, user-scalable=no&quot; /&gt;
            &lt;style&gt;
                #map_352013499f790bffa267deaf6984128b {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
                .leaflet-container { font-size: 1rem; }
            &lt;/style&gt;


                    &lt;style&gt;
                        .foliumtooltip {

                        }
                       .foliumtooltip table{
                            margin: auto;
                        }
                        .foliumtooltip tr{
                            text-align: left;
                        }
                        .foliumtooltip th{
                            padding: 2px; padding-right: 8px;
                        }
                    &lt;/style&gt;

&lt;/head&gt;
&lt;body&gt;


            &lt;div class=&quot;folium-map&quot; id=&quot;map_352013499f790bffa267deaf6984128b&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;


            var map_352013499f790bffa267deaf6984128b = L.map(
                &quot;map_352013499f790bffa267deaf6984128b&quot;,
                {
                    center: [8.881675243377686, 7.187414884567261],
                    crs: L.CRS.EPSG3857,
                    zoom: 10,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );
            L.control.scale().addTo(map_352013499f790bffa267deaf6984128b);





            var tile_layer_5ab64c720a3905aeea41bbcd5a5b7a4d = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca target=\&quot;_blank\&quot; href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca target=\&quot;_blank\&quot; href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_352013499f790bffa267deaf6984128b);


            map_352013499f790bffa267deaf6984128b.fitBounds(
                [[8.403410911560059, 6.746333122253418], [9.359939575195312, 7.6284966468811035]],
                {}
            );


        function geo_json_35a3489f5976a6d77cc9471066ff93e3_styler(feature) {
            switch(feature.id) {
                default:
                    return {&quot;fillOpacity&quot;: 0.5, &quot;weight&quot;: 2};
            }
        }
        function geo_json_35a3489f5976a6d77cc9471066ff93e3_highlighter(feature) {
            switch(feature.id) {
                default:
                    return {&quot;fillOpacity&quot;: 0.75};
            }
        }
        function geo_json_35a3489f5976a6d77cc9471066ff93e3_pointToLayer(feature, latlng) {
            var opts = {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 2, &quot;stroke&quot;: true, &quot;weight&quot;: 3};

            let style = geo_json_35a3489f5976a6d77cc9471066ff93e3_styler(feature)
            Object.assign(opts, style)

            return new L.CircleMarker(latlng, opts)
        }

        function geo_json_35a3489f5976a6d77cc9471066ff93e3_onEachFeature(feature, layer) {
            layer.on({
                mouseout: function(e) {
                    if(typeof e.target.setStyle === &quot;function&quot;){
                        geo_json_35a3489f5976a6d77cc9471066ff93e3.resetStyle(e.target);
                    }
                },
                mouseover: function(e) {
                    if(typeof e.target.setStyle === &quot;function&quot;){
                        const highlightStyle = geo_json_35a3489f5976a6d77cc9471066ff93e3_highlighter(e.target.feature)
                        e.target.setStyle(highlightStyle);
                    }
                },
            });
        };
        var geo_json_35a3489f5976a6d77cc9471066ff93e3 = L.geoJson(null, {
                onEachFeature: geo_json_35a3489f5976a6d77cc9471066ff93e3_onEachFeature,

                style: geo_json_35a3489f5976a6d77cc9471066ff93e3_styler,
                pointToLayer: geo_json_35a3489f5976a6d77cc9471066ff93e3_pointToLayer
        });

        function geo_json_35a3489f5976a6d77cc9471066ff93e3_add (data) {
            geo_json_35a3489f5976a6d77cc9471066ff93e3
                .addData(data)
                .addTo(map_352013499f790bffa267deaf6984128b);
        }
            geo_json_35a3489f5976a6d77cc9471066ff93e3_add({&quot;bbox&quot;: [6.746333122253418, 8.403410911560059, 7.6284966468811035, 9.359939575195312], &quot;features&quot;: [{&quot;bbox&quot;: [7.1702728271484375, 9.044351577758789, 7.6284966468811035, 9.359939575195312], &quot;geometry&quot;: {&quot;coordinates&quot;: [[[7.621428489685059, 9.307660102844238], [7.622406482696533, 9.308396339416504], [7.62583589553833, 9.310739517211914], [7.626468658447266, 9.297344207763672], [7.628000259399414, 9.292647361755371], [7.6284966468811035, 9.29112434387207], [7.624773979187012, 9.27168083190918], [7.619063854217529, 9.26338005065918], [7.617894649505615, 9.261680603027344], [7.606660842895508, 9.26095199584961], [7.601244926452637, 9.260600090026855], [7.600341320037842, 9.259556770324707], [7.592771053314209, 9.250815391540527], [7.589991569519043, 9.242437362670898], [7.5846967697143555, 9.226478576660156], [7.576173782348633, 9.203986167907715], [7.572071552276611, 9.185931205749512], [7.571070671081543, 9.181526184082031], [7.564399719238281, 9.166213989257812], [7.558621406555176, 9.14605712890625], [7.55898904800415, 9.138708114624023], [7.559465408325195, 9.129182815551758], [7.559892177581787, 9.12065315246582], [7.559083461761475, 9.115644454956055], [7.557531356811523, 9.106033325195312], [7.556155204772949, 9.097512245178223], [7.5567498207092285, 9.091827392578125], [7.556956768035889, 9.089850425720215], [7.55813455581665, 9.078584671020508], [7.557452201843262, 9.058537483215332], [7.557402610778809, 9.05849838256836], [7.557373046875, 9.058475494384766], [7.555658340454102, 9.05672836303711], [7.552108287811279, 9.05311107635498], [7.5495219230651855, 9.052023887634277], [7.5447998046875, 9.050039291381836], [7.539083003997803, 9.045825958251953], [7.529728412628174, 9.044351577758789], [7.526663303375244, 9.045073509216309], [7.5231218338012695, 9.045907020568848], [7.521278381347656, 9.045978546142578], [7.51833438873291, 9.046092987060547], [7.511046886444092, 9.048566818237305], [7.505588531494141, 9.05221176147461], [7.5040059089660645, 9.055663108825684], [7.504948139190674, 9.063528060913086], [7.500728607177734, 9.071961402893066], [7.498159408569336, 9.077095985412598], [7.500064849853516, 9.078654289245605], [7.500446796417236, 9.07896614074707], [7.499958515167236, 9.083046913146973], [7.499783992767334, 9.084505081176758], [7.499203681945801, 9.08548641204834], [7.497065544128418, 9.08910083770752], [7.495716094970703, 9.090753555297852], [7.48957633972168, 9.098273277282715], [7.484334945678711, 9.09914779663086], [7.474109172821045, 9.108294486999512], [7.469807147979736, 9.111729621887207], [7.465243816375732, 9.115373611450195], [7.459084987640381, 9.114853858947754], [7.454306125640869, 9.117350578308105], [7.449864864349365, 9.119132995605469], [7.444744110107422, 9.121188163757324], [7.440614700317383, 9.120365142822266], [7.437441825866699, 9.119732856750488], [7.427877426147461, 9.122878074645996], [7.4247517585754395, 9.12387466430664], [7.4201340675354, 9.125347137451172], [7.415127277374268, 9.125700950622559], [7.411471843719482, 9.125958442687988], [7.408050537109375, 9.12569522857666], [7.400766849517822, 9.129093170166016], [7.3985185623168945, 9.129254341125488], [7.3950676918029785, 9.129501342773438], [7.391879081726074, 9.130395889282227], [7.3849616050720215, 9.132149696350098], [7.379578113555908, 9.133515357971191], [7.375432968139648, 9.13494873046875], [7.370469093322754, 9.136664390563965], [7.367551803588867, 9.136495590209961], [7.365679740905762, 9.136387825012207], [7.363100051879883, 9.136537551879883], [7.358840465545654, 9.136785507202148], [7.358614444732666, 9.137246131896973], [7.354060173034668, 9.13882064819336], [7.351781368255615, 9.139707565307617], [7.347684860229492, 9.141302108764648], [7.342232704162598, 9.146565437316895], [7.334492206573486, 9.149726867675781], [7.3286662101745605, 9.152107238769531], [7.326751708984375, 9.152889251708984], [7.32275915145874, 9.152852058410645], [7.316034317016602, 9.152789115905762], [7.306233882904053, 9.153851509094238], [7.299670696258545, 9.154441833496094], [7.2946085929870605, 9.154897689819336], [7.284098148345947, 9.149253845214844], [7.279747009277344, 9.144360542297363], [7.272644996643066, 9.13574504852295], [7.266247272491455, 9.132218360900879], [7.263846397399902, 9.130982398986816], [7.258479118347168, 9.128217697143555], [7.2582688331604, 9.128107070922852], [7.247740268707275, 9.12257194519043], [7.2381463050842285, 9.118091583251953], [7.232428073883057, 9.11341667175293], [7.226021766662598, 9.107810974121094], [7.224567890167236, 9.1067476272583], [7.218018531799316, 9.101959228515625], [7.213973522186279, 9.099814414978027], [7.210478782653809, 9.097960472106934], [7.205221652984619, 9.09467601776123], [7.192665100097656, 9.090630531311035], [7.189242839813232, 9.09013557434082], [7.1877570152282715, 9.090624809265137], [7.183777332305908, 9.091933250427246], [7.181204319000244, 9.091636657714844], [7.179426670074463, 9.091431617736816], [7.178225994110107, 9.091805458068848], [7.175095558166504, 9.092780113220215], [7.171948432922363, 9.096681594848633], [7.170467853546143, 9.098173141479492], [7.170356273651123, 9.098285675048828], [7.1702728271484375, 9.099250793457031], [7.171046733856201, 9.099447250366211], [7.177483081817627, 9.110136032104492], [7.185293674468994, 9.121781349182129], [7.1859049797058105, 9.122692108154297], [7.197359561920166, 9.141053199768066], [7.206053733825684, 9.150837898254395], [7.210739612579346, 9.156195640563965], [7.224045276641846, 9.173879623413086], [7.235055923461914, 9.191774368286133], [7.244218349456787, 9.202486991882324], [7.252147197723389, 9.213651657104492], [7.261308670043945, 9.228294372558594], [7.271813869476318, 9.244566917419434], [7.276005744934082, 9.252462387084961], [7.284096717834473, 9.258774757385254], [7.2944536209106445, 9.276201248168945], [7.298645496368408, 9.284096717834473], [7.303959369659424, 9.293389320373535], [7.308836936950684, 9.297362327575684], [7.310853481292725, 9.297494888305664], [7.3129425048828125, 9.297632217407227], [7.322061538696289, 9.297255516052246], [7.325264930725098, 9.300058364868164], [7.3387346267700195, 9.304344177246094], [7.363357067108154, 9.303420066833496], [7.378868103027344, 9.304952621459961], [7.394613742828369, 9.308103561401367], [7.409660816192627, 9.307552337646484], [7.432682991027832, 9.305458068847656], [7.451604843139648, 9.30448055267334], [7.46140193939209, 9.30249309539795], [7.475270748138428, 9.29199504852295], [7.485975742340088, 9.288860321044922], [7.495566368103027, 9.2924165725708], [7.504269599914551, 9.302433967590332], [7.512065887451172, 9.313828468322754], [7.515970706939697, 9.321259498596191], [7.525836944580078, 9.337294578552246], [7.5334153175354, 9.351229667663574], [7.541645526885986, 9.356852531433105], [7.55055046081543, 9.359939575195312], [7.557157516479492, 9.35861587524414], [7.567852973937988, 9.35293960571289], [7.57442569732666, 9.34260368347168], [7.577127456665039, 9.3336181640625], [7.581187725067139, 9.322103500366211], [7.5832061767578125, 9.313342094421387], [7.589794158935547, 9.306934356689453], [7.5916032791137695, 9.303023338317871], [7.604868412017822, 9.298728942871094], [7.605725288391113, 9.29845142364502], [7.606861591339111, 9.298084259033203], [7.616911888122559, 9.302568435668945], [7.621428489685059, 9.307660102844238]]], &quot;type&quot;: &quot;Polygon&quot;}, &quot;id&quot;: &quot;300&quot;, &quot;properties&quot;: {&quot;AREA&quot;: 818.909, &quot;FULL_NAME&quot;: null, &quot;LATITUDE&quot;: null, &quot;LGA&quot;: &quot;Bwari&quot;, &quot;LONGITUDE&quot;: null, &quot;PERIMETER&quot;: 153.605, &quot;STATE&quot;: &quot;Abuja&quot;}, &quot;type&quot;: &quot;Feature&quot;}, {&quot;bbox&quot;: [6.823147296905518, 8.889777183532715, 7.166009426116943, 9.235713005065918], &quot;geometry&quot;: {&quot;coordinates&quot;: [[[7.120201110839844, 9.126890182495117], [7.1267876625061035, 9.118703842163086], [7.137685298919678, 9.106780052185059], [7.145867347717285, 9.098994255065918], [7.1559600830078125, 9.095616340637207], [7.163837432861328, 9.097616195678711], [7.1639018058776855, 9.09763240814209], [7.163971424102783, 9.097650527954102], [7.164676189422607, 9.096222877502441], [7.165372371673584, 9.088432312011719], [7.165672779083252, 9.08506965637207], [7.166009426116943, 9.07559585571289], [7.163594722747803, 9.065635681152344], [7.162614822387695, 9.063135147094727], [7.162140369415283, 9.06192398071289], [7.161630630493164, 9.061182975769043], [7.1608710289001465, 9.05775260925293], [7.160669326782227, 9.056941986083984], [7.159873962402344, 9.053744316101074], [7.161081314086914, 9.052716255187988], [7.161052703857422, 9.0526704788208], [7.158161163330078, 9.046521186828613], [7.158087253570557, 9.046363830566406], [7.157974720001221, 9.045899391174316], [7.1573686599731445, 9.043394088745117], [7.156249046325684, 9.04196834564209], [7.1556806564331055, 9.041244506835938], [7.15461540222168, 9.03897762298584], [7.150493144989014, 9.034317970275879], [7.14727258682251, 9.026894569396973], [7.14292049407959, 9.021770477294922], [7.136971950531006, 9.016631126403809], [7.135768413543701, 9.015036582946777], [7.134974002838135, 9.013984680175781], [7.1317009925842285, 9.009649276733398], [7.130025863647461, 9.007782936096191], [7.128356456756592, 9.005922317504883], [7.127404689788818, 9.004600524902344], [7.127126216888428, 9.0042142868042], [7.1270246505737305, 9.004073143005371], [7.126990795135498, 9.004026412963867], [7.125895023345947, 9.0025053024292], [7.123831272125244, 8.999641418457031], [7.123340129852295, 8.998787879943848], [7.123060703277588, 8.998302459716797], [7.122759819030762, 8.997779846191406], [7.124849796295166, 8.99777603149414], [7.124354362487793, 8.99704647064209], [7.124336242675781, 8.99701976776123], [7.124171733856201, 8.99677848815918], [7.117486953735352, 8.98283576965332], [7.10786771774292, 8.972860336303711], [7.097280979156494, 8.960136413574219], [7.090384483337402, 8.947349548339844], [7.080552577972412, 8.938529968261719], [7.072328090667725, 8.929222106933594], [7.066224098205566, 8.922643661499023], [7.06335973739624, 8.916701316833496], [7.060952663421631, 8.910520553588867], [7.070489883422852, 8.901142120361328], [7.071489334106445, 8.901070594787598], [7.072854995727539, 8.900973320007324], [7.072434902191162, 8.900299072265625], [7.071371078491211, 8.898592948913574], [7.066453456878662, 8.894067764282227], [7.059737205505371, 8.891996383666992], [7.053702354431152, 8.890135765075684], [7.034088611602783, 8.889777183532715], [7.015452861785889, 8.892857551574707], [7.003241539001465, 8.908041954040527], [6.991908073425293, 8.920446395874023], [6.97157621383667, 8.918486595153809], [6.942361354827881, 8.909303665161133], [6.9207868576049805, 8.901834487915039], [6.905059814453125, 8.899335861206055], [6.884523391723633, 8.898992538452148], [6.873271465301514, 8.902178764343262], [6.861167907714844, 8.909757614135742], [6.854801177978516, 8.91401195526123], [6.8549323081970215, 8.914518356323242], [6.855514049530029, 8.916765213012695], [6.855867862701416, 8.924363136291504], [6.851329803466797, 8.929048538208008], [6.85132360458374, 8.936899185180664], [6.851318836212158, 8.942643165588379], [6.848492622375488, 8.953290939331055], [6.84729528427124, 8.964831352233887], [6.847487449645996, 8.967083930969238], [6.848433494567871, 8.978177070617676], [6.849812030792236, 8.986673355102539], [6.8504838943481445, 8.990815162658691], [6.850449562072754, 8.99280071258545], [6.850348949432373, 8.998648643493652], [6.854900360107422, 8.998701095581055], [6.854478359222412, 9.000038146972656], [6.852832317352295, 9.00401496887207], [6.851289749145508, 9.007742881774902], [6.850897312164307, 9.00869083404541], [6.842442512512207, 9.023658752441406], [6.838243007659912, 9.034355163574219], [6.840795516967773, 9.044961929321289], [6.844248294830322, 9.053750991821289], [6.848376274108887, 9.058653831481934], [6.855942726135254, 9.070740699768066], [6.854608058929443, 9.080119132995605], [6.850751876831055, 9.087152481079102], [6.846986293792725, 9.090287208557129], [6.837571620941162, 9.095599174499512], [6.829987049102783, 9.10049057006836], [6.823147296905518, 9.109526634216309], [6.828204154968262, 9.115825653076172], [6.827600479125977, 9.124532699584961], [6.829379558563232, 9.13056468963623], [6.834251880645752, 9.135224342346191], [6.841784954071045, 9.138818740844727], [6.851562023162842, 9.141544342041016], [6.859800815582275, 9.144696235656738], [6.8627214431762695, 9.14980411529541], [6.867778301239014, 9.156103134155273], [6.868140697479248, 9.164140701293945], [6.8709282875061035, 9.168327331542969], [6.877030849456787, 9.170289039611816], [6.894341468811035, 9.171792984008789], [6.908311367034912, 9.17735767364502], [6.914866924285889, 9.18345832824707], [6.916130542755127, 9.189678192138672], [6.918971538543701, 9.195243835449219], [6.91889762878418, 9.205795288085938], [6.921220779418945, 9.209284782409668], [6.933000564575195, 9.212281227111816], [6.942488670349121, 9.220047950744629], [6.950276851654053, 9.224108695983887], [6.961634635925293, 9.231225967407227], [6.963187217712402, 9.232404708862305], [6.979012966156006, 9.235713005065918], [6.991518974304199, 9.23115348815918], [7.007513523101807, 9.225592613220215], [7.016153335571289, 9.214573860168457], [7.036712169647217, 9.1990385055542], [7.0495710372924805, 9.188519477844238], [7.063132286071777, 9.179393768310547], [7.073759078979492, 9.166312217712402], [7.090111255645752, 9.150507926940918], [7.104808330535889, 9.139081001281738], [7.117740631103516, 9.129949569702148], [7.120201110839844, 9.126890182495117]]], &quot;type&quot;: &quot;Polygon&quot;}, &quot;id&quot;: &quot;307&quot;, &quot;properties&quot;: {&quot;AREA&quot;: 967.762, &quot;FULL_NAME&quot;: null, &quot;LATITUDE&quot;: null, &quot;LGA&quot;: &quot;Gwagwalada&quot;, &quot;LONGITUDE&quot;: null, &quot;PERIMETER&quot;: 129.429, &quot;STATE&quot;: &quot;Abuja&quot;}, &quot;type&quot;: &quot;Feature&quot;}, {&quot;bbox&quot;: [6.746333122253418, 8.403410911560059, 6.985164642333984, 9.232657432556152], &quot;geometry&quot;: {&quot;coordinates&quot;: [[[6.963187217712402, 9.232404708862305], [6.961634635925293, 9.231225967407227], [6.950276851654053, 9.224108695983887], [6.942488670349121, 9.220047950744629], [6.933000564575195, 9.212281227111816], [6.921220779418945, 9.209284782409668], [6.91889762878418, 9.205795288085938], [6.918971538543701, 9.195243835449219], [6.916130542755127, 9.189678192138672], [6.914866924285889, 9.18345832824707], [6.908311367034912, 9.17735767364502], [6.894341468811035, 9.171792984008789], [6.877030849456787, 9.170289039611816], [6.8709282875061035, 9.168327331542969], [6.868140697479248, 9.164140701293945], [6.867778301239014, 9.156103134155273], [6.8627214431762695, 9.14980411529541], [6.859800815582275, 9.144696235656738], [6.851562023162842, 9.141544342041016], [6.841784954071045, 9.138818740844727], [6.834251880645752, 9.135224342346191], [6.829379558563232, 9.13056468963623], [6.827600479125977, 9.124532699584961], [6.828204154968262, 9.115825653076172], [6.823147296905518, 9.109526634216309], [6.829987049102783, 9.10049057006836], [6.837571620941162, 9.095599174499512], [6.846986293792725, 9.090287208557129], [6.850751876831055, 9.087152481079102], [6.854608058929443, 9.080119132995605], [6.855942726135254, 9.070740699768066], [6.848376274108887, 9.058653831481934], [6.844248294830322, 9.053750991821289], [6.840795516967773, 9.044961929321289], [6.838243007659912, 9.034355163574219], [6.842442512512207, 9.023658752441406], [6.850897312164307, 9.00869083404541], [6.851289749145508, 9.007742881774902], [6.852832317352295, 9.00401496887207], [6.854478359222412, 9.000038146972656], [6.854900360107422, 8.998701095581055], [6.850348949432373, 8.998648643493652], [6.850449562072754, 8.99280071258545], [6.8504838943481445, 8.990815162658691], [6.849812030792236, 8.986673355102539], [6.848433494567871, 8.978177070617676], [6.847487449645996, 8.967083930969238], [6.84729528427124, 8.964831352233887], [6.848492622375488, 8.953290939331055], [6.851318836212158, 8.942643165588379], [6.85132360458374, 8.936899185180664], [6.851329803466797, 8.929048538208008], [6.855867862701416, 8.924363136291504], [6.855514049530029, 8.916765213012695], [6.8549323081970215, 8.914518356323242], [6.854801177978516, 8.91401195526123], [6.854711055755615, 8.914072036743164], [6.854093074798584, 8.914484977722168], [6.852778911590576, 8.912459373474121], [6.850544452667236, 8.909015655517578], [6.85053825378418, 8.903972625732422], [6.850525379180908, 8.893577575683594], [6.8513689041137695, 8.874439239501953], [6.849334716796875, 8.864961624145508], [6.847377300262451, 8.855842590332031], [6.8442206382751465, 8.846237182617188], [6.840826034545898, 8.835906982421875], [6.832564353942871, 8.821863174438477], [6.830626010894775, 8.818568229675293], [6.8262104988098145, 8.81126880645752], [6.82138204574585, 8.8032865524292], [6.816810131072998, 8.799225807189941], [6.806870937347412, 8.790398597717285], [6.806248188018799, 8.789917945861816], [6.796359062194824, 8.782281875610352], [6.794849872589111, 8.779057502746582], [6.789190769195557, 8.76696491241455], [6.788330554962158, 8.742324829101562], [6.793515205383301, 8.72057819366455], [6.794068813323975, 8.69775676727295], [6.7902703285217285, 8.67685317993164], [6.78761625289917, 8.672038078308105], [6.78599214553833, 8.66909122467041], [6.784560203552246, 8.666703224182129], [6.782435894012451, 8.663161277770996], [6.786464214324951, 8.655488967895508], [6.790481090545654, 8.647126197814941], [6.789302349090576, 8.642961502075195], [6.7873005867004395, 8.635889053344727], [6.785288333892822, 8.630322456359863], [6.784402847290039, 8.627873420715332], [6.781259059906006, 8.618940353393555], [6.787306785583496, 8.59349250793457], [6.79224967956543, 8.585343360900879], [6.7916131019592285, 8.5602388381958], [6.802844047546387, 8.556137084960938], [6.808812618255615, 8.553956985473633], [6.839366912841797, 8.547006607055664], [6.854780197143555, 8.543500900268555], [6.861478328704834, 8.544007301330566], [6.87718391418457, 8.545194625854492], [6.892613410949707, 8.543550491333008], [6.897485256195068, 8.541220664978027], [6.909095287322998, 8.53566837310791], [6.922162055969238, 8.52794075012207], [6.9271464347839355, 8.524992942810059], [6.936943054199219, 8.513771057128906], [6.941641807556152, 8.50838851928711], [6.949065685272217, 8.505997657775879], [6.959771156311035, 8.502551078796387], [6.967479228973389, 8.490447044372559], [6.972510814666748, 8.482545852661133], [6.973269462585449, 8.481354713439941], [6.983405590057373, 8.480491638183594], [6.985164642333984, 8.4751615524292], [6.984955787658691, 8.471471786499023], [6.98387336730957, 8.452372550964355], [6.978899955749512, 8.444392204284668], [6.976682662963867, 8.435673713684082], [6.975081920623779, 8.430357933044434], [6.969207286834717, 8.410850524902344], [6.964859485626221, 8.410382270812988], [6.955526828765869, 8.404313087463379], [6.949889659881592, 8.404489517211914], [6.93937349319458, 8.404818534851074], [6.923897743225098, 8.404390335083008], [6.908689975738525, 8.406264305114746], [6.896263122558594, 8.408782958984375], [6.884654998779297, 8.407411575317383], [6.882615566253662, 8.407170295715332], [6.874965190887451, 8.405224800109863], [6.874359607696533, 8.404444694519043], [6.874258041381836, 8.404314041137695], [6.867114067077637, 8.40512752532959], [6.858612537384033, 8.404886245727539], [6.856950759887695, 8.404839515686035], [6.846820831298828, 8.40662670135498], [6.837826728820801, 8.407241821289062], [6.834949016571045, 8.40681266784668], [6.825329303741455, 8.405378341674805], [6.814477443695068, 8.405332565307617], [6.808162212371826, 8.403989791870117], [6.8054423332214355, 8.403410911560059], [6.803096771240234, 8.40415096282959], [6.799155235290527, 8.405394554138184], [6.796942710876465, 8.406092643737793], [6.794879913330078, 8.406668663024902], [6.793996810913086, 8.406914710998535], [6.791656494140625, 8.407567024230957], [6.791557312011719, 8.40760326385498], [6.78247594833374, 8.410953521728516], [6.777463436126709, 8.423515319824219], [6.7662672996521, 8.451574325561523], [6.75096321105957, 8.489928245544434], [6.751011371612549, 8.492934226989746], [6.751035690307617, 8.494431495666504], [6.751067161560059, 8.496386528015137], [6.751374244689941, 8.498162269592285], [6.752297878265381, 8.503504753112793], [6.752339839935303, 8.50374698638916], [6.757290840148926, 8.510122299194336], [6.757582187652588, 8.510571479797363], [6.758474826812744, 8.511947631835938], [6.758076190948486, 8.515613555908203], [6.757871627807617, 8.517495155334473], [6.757696151733398, 8.51795482635498], [6.7574286460876465, 8.518655776977539], [6.7513275146484375, 8.971392631530762], [6.74824857711792, 8.980903625488281], [6.748628616333008, 8.98276424407959], [6.748711585998535, 8.983169555664062], [6.749000549316406, 8.984582901000977], [6.748908042907715, 8.984583854675293], [6.748769283294678, 8.984586715698242], [6.748308181762695, 8.984594345092773], [6.7482218742370605, 8.98898696899414], [6.748181343078613, 8.991056442260742], [6.746333122253418, 8.997467994689941], [6.747630596160889, 8.997461318969727], [6.747409343719482, 9.000711441040039], [6.7471842765808105, 9.004011154174805], [6.746846675872803, 9.008964538574219], [6.747849941253662, 9.031898498535156], [6.74912691116333, 9.041916847229004], [6.749504089355469, 9.044877052307129], [6.750168800354004, 9.06039810180664], [6.749986171722412, 9.066657066345215], [6.749553203582764, 9.081469535827637], [6.750303745269775, 9.105327606201172], [6.7514777183532715, 9.118766784667969], [6.751153945922852, 9.12830638885498], [6.751018047332764, 9.132304191589355], [6.750974655151367, 9.133585929870605], [6.75030517578125, 9.157668113708496], [6.7495646476745605, 9.185687065124512], [6.749773979187012, 9.200279235839844], [6.751780986785889, 9.206774711608887], [6.752073287963867, 9.216734886169434], [6.757628440856934, 9.22278881072998], [6.76703405380249, 9.224696159362793], [6.7783522605896, 9.225224494934082], [6.788949966430664, 9.22644329071045], [6.800955295562744, 9.228134155273438], [6.808030128479004, 9.228405952453613], [6.817696571350098, 9.228924751281738], [6.834935188293457, 9.2283296585083], [6.856867790222168, 9.229150772094727], [6.866745471954346, 9.231060981750488], [6.875965118408203, 9.23018741607666], [6.887556552886963, 9.228632926940918], [6.9060564041137695, 9.229162216186523], [6.920607566833496, 9.229691505432129], [6.930928707122803, 9.232657432556152], [6.940836429595947, 9.231255531311035], [6.963187217712402, 9.232404708862305]]], &quot;type&quot;: &quot;Polygon&quot;}, &quot;id&quot;: &quot;308&quot;, &quot;properties&quot;: {&quot;AREA&quot;: 1002.453, &quot;FULL_NAME&quot;: null, &quot;LATITUDE&quot;: null, &quot;LGA&quot;: &quot;Abaji&quot;, &quot;LONGITUDE&quot;: null, &quot;PERIMETER&quot;: 257.578, &quot;STATE&quot;: &quot;Abuja&quot;}, &quot;type&quot;: &quot;Feature&quot;}, {&quot;bbox&quot;: [7.060952663421631, 8.625009536743164, 7.565664768218994, 9.154897689819336], &quot;geometry&quot;: {&quot;coordinates&quot;: [[[7.1702728271484375, 9.099250793457031], [7.170356273651123, 9.098285675048828], [7.170467853546143, 9.098173141479492], [7.171948432922363, 9.096681594848633], [7.175095558166504, 9.092780113220215], [7.178225994110107, 9.091805458068848], [7.179426670074463, 9.091431617736816], [7.181204319000244, 9.091636657714844], [7.183777332305908, 9.091933250427246], [7.1877570152282715, 9.090624809265137], [7.189242839813232, 9.09013557434082], [7.192665100097656, 9.090630531311035], [7.205221652984619, 9.09467601776123], [7.210478782653809, 9.097960472106934], [7.213973522186279, 9.099814414978027], [7.218018531799316, 9.101959228515625], [7.224567890167236, 9.1067476272583], [7.226021766662598, 9.107810974121094], [7.232428073883057, 9.11341667175293], [7.2381463050842285, 9.118091583251953], [7.247740268707275, 9.12257194519043], [7.2582688331604, 9.128107070922852], [7.258479118347168, 9.128217697143555], [7.263846397399902, 9.130982398986816], [7.266247272491455, 9.132218360900879], [7.272644996643066, 9.13574504852295], [7.279747009277344, 9.144360542297363], [7.284098148345947, 9.149253845214844], [7.2946085929870605, 9.154897689819336], [7.299670696258545, 9.154441833496094], [7.306233882904053, 9.153851509094238], [7.316034317016602, 9.152789115905762], [7.32275915145874, 9.152852058410645], [7.326751708984375, 9.152889251708984], [7.3286662101745605, 9.152107238769531], [7.334492206573486, 9.149726867675781], [7.342232704162598, 9.146565437316895], [7.347684860229492, 9.141302108764648], [7.351781368255615, 9.139707565307617], [7.354060173034668, 9.13882064819336], [7.358614444732666, 9.137246131896973], [7.358840465545654, 9.136785507202148], [7.363100051879883, 9.136537551879883], [7.365679740905762, 9.136387825012207], [7.367551803588867, 9.136495590209961], [7.370469093322754, 9.136664390563965], [7.375432968139648, 9.13494873046875], [7.379578113555908, 9.133515357971191], [7.3849616050720215, 9.132149696350098], [7.391879081726074, 9.130395889282227], [7.3950676918029785, 9.129501342773438], [7.3985185623168945, 9.129254341125488], [7.400766849517822, 9.129093170166016], [7.408050537109375, 9.12569522857666], [7.411471843719482, 9.125958442687988], [7.415127277374268, 9.125700950622559], [7.4201340675354, 9.125347137451172], [7.4247517585754395, 9.12387466430664], [7.427877426147461, 9.122878074645996], [7.437441825866699, 9.119732856750488], [7.440614700317383, 9.120365142822266], [7.444744110107422, 9.121188163757324], [7.449864864349365, 9.119132995605469], [7.454306125640869, 9.117350578308105], [7.459084987640381, 9.114853858947754], [7.465243816375732, 9.115373611450195], [7.469807147979736, 9.111729621887207], [7.474109172821045, 9.108294486999512], [7.484334945678711, 9.09914779663086], [7.48957633972168, 9.098273277282715], [7.495716094970703, 9.090753555297852], [7.497065544128418, 9.08910083770752], [7.499203681945801, 9.08548641204834], [7.499783992767334, 9.084505081176758], [7.499958515167236, 9.083046913146973], [7.500446796417236, 9.07896614074707], [7.500064849853516, 9.078654289245605], [7.498159408569336, 9.077095985412598], [7.500728607177734, 9.071961402893066], [7.504948139190674, 9.063528060913086], [7.5040059089660645, 9.055663108825684], [7.505588531494141, 9.05221176147461], [7.511046886444092, 9.048566818237305], [7.51833438873291, 9.046092987060547], [7.521278381347656, 9.045978546142578], [7.5231218338012695, 9.045907020568848], [7.526663303375244, 9.045073509216309], [7.529728412628174, 9.044351577758789], [7.539083003997803, 9.045825958251953], [7.5447998046875, 9.050039291381836], [7.5495219230651855, 9.052023887634277], [7.552108287811279, 9.05311107635498], [7.555658340454102, 9.05672836303711], [7.557373046875, 9.058475494384766], [7.557402610778809, 9.05849838256836], [7.557452201843262, 9.058537483215332], [7.557363510131836, 9.055933952331543], [7.559606552124023, 9.045412063598633], [7.559614658355713, 9.045374870300293], [7.561395168304443, 9.03702449798584], [7.56247615814209, 9.032880783081055], [7.563408374786377, 9.029308319091797], [7.565664768218994, 9.020660400390625], [7.565157890319824, 9.007485389709473], [7.563965797424316, 9.004044532775879], [7.563564777374268, 9.004044532775879], [7.562891483306885, 9.004044532775879], [7.562423229217529, 9.000150680541992], [7.562320232391357, 8.999296188354492], [7.561944961547852, 8.998213768005371], [7.561987400054932, 8.997783660888672], [7.56206750869751, 8.99696159362793], [7.562118053436279, 8.99696159362793], [7.562148094177246, 8.99696159362793], [7.562819957733154, 8.968865394592285], [7.558993816375732, 8.957382202148438], [7.557240009307861, 8.952118873596191], [7.559694290161133, 8.932467460632324], [7.556765079498291, 8.908293724060059], [7.551255702972412, 8.895930290222168], [7.546412944793701, 8.881939888000488], [7.544887065887451, 8.873199462890625], [7.542881011962891, 8.863313674926758], [7.537911415100098, 8.855785369873047], [7.534427165985107, 8.840157508850098], [7.529469013214111, 8.833320617675781], [7.526829719543457, 8.827136993408203], [7.524696350097656, 8.823712348937988], [7.523777484893799, 8.80965518951416], [7.522965431213379, 8.807520866394043], [7.519679069519043, 8.798882484436035], [7.517922401428223, 8.790145874023438], [7.515856742858887, 8.776569366455078], [7.515209674835205, 8.765046119689941], [7.513396739959717, 8.752849578857422], [7.509890556335449, 8.735837936401367], [7.506965160369873, 8.726198196411133], [7.50269889831543, 8.71934986114502], [7.500242710113525, 8.695859909057617], [7.487451553344727, 8.690079689025879], [7.485273838043213, 8.68388843536377], [7.48022985458374, 8.671747207641602], [7.475316524505615, 8.653374671936035], [7.471071243286133, 8.639214515686035], [7.468782901763916, 8.63840103149414], [7.464780330657959, 8.633399963378906], [7.457813262939453, 8.630521774291992], [7.449700355529785, 8.628125190734863], [7.442543983459473, 8.627785682678223], [7.43723201751709, 8.62741470336914], [7.4295806884765625, 8.625009536743164], [7.425001621246338, 8.627161026000977], [7.415352821350098, 8.62962818145752], [7.411786079406738, 8.637292861938477], [7.415807247161865, 8.64344596862793], [7.423484802246094, 8.64746379852295], [7.435640811920166, 8.65739631652832], [7.445223331451416, 8.665067672729492], [7.4550957679748535, 8.676421165466309], [7.464439868927002, 8.683636665344238], [7.474259853363037, 8.691764831542969], [7.480855464935303, 8.700179100036621], [7.482577800750732, 8.706831932067871], [7.484251976013184, 8.710490226745605], [7.488287925720215, 8.717564582824707], [7.491914749145508, 8.727871894836426], [7.495515823364258, 8.736567497253418], [7.497740745544434, 8.745746612548828], [7.500143527984619, 8.751696586608887], [7.500400543212891, 8.753305435180664], [7.497640132904053, 8.753812789916992], [7.4935078620910645, 8.755034446716309], [7.486804962158203, 8.754226684570312], [7.475329875946045, 8.757877349853516], [7.462266445159912, 8.763167381286621], [7.457765579223633, 8.770156860351562], [7.453982830047607, 8.778745651245117], [7.456523895263672, 8.793219566345215], [7.452775001525879, 8.803881645202637], [7.449341773986816, 8.805553436279297], [7.44106912612915, 8.807536125183105], [7.427894115447998, 8.805916786193848], [7.413561820983887, 8.804085731506348], [7.390507221221924, 8.804936408996582], [7.373965740203857, 8.809134483337402], [7.358220100402832, 8.8054838180542], [7.341124534606934, 8.803930282592773], [7.31696891784668, 8.808025360107422], [7.272872447967529, 8.820063591003418], [7.226443767547607, 8.844813346862793], [7.191192626953125, 8.861769676208496], [7.162153720855713, 8.877699851989746], [7.151861190795898, 8.883173942565918], [7.14108943939209, 8.887503623962402], [7.128196716308594, 8.889104843139648], [7.115103721618652, 8.892552375793457], [7.103911399841309, 8.89942455291748], [7.100920677185059, 8.899935722351074], [7.083162784576416, 8.900236129760742], [7.072854995727539, 8.900973320007324], [7.071489334106445, 8.901070594787598], [7.070489883422852, 8.901142120361328], [7.060952663421631, 8.910520553588867], [7.06335973739624, 8.916701316833496], [7.066224098205566, 8.922643661499023], [7.072328090667725, 8.929222106933594], [7.080552577972412, 8.938529968261719], [7.090384483337402, 8.947349548339844], [7.097280979156494, 8.960136413574219], [7.10786771774292, 8.972860336303711], [7.117486953735352, 8.98283576965332], [7.124171733856201, 8.99677848815918], [7.124336242675781, 8.99701976776123], [7.124354362487793, 8.99704647064209], [7.124849796295166, 8.99777603149414], [7.122759819030762, 8.997779846191406], [7.123060703277588, 8.998302459716797], [7.123340129852295, 8.998787879943848], [7.123831272125244, 8.999641418457031], [7.125895023345947, 9.0025053024292], [7.126990795135498, 9.004026412963867], [7.1270246505737305, 9.004073143005371], [7.127126216888428, 9.0042142868042], [7.127404689788818, 9.004600524902344], [7.128356456756592, 9.005922317504883], [7.130025863647461, 9.007782936096191], [7.1317009925842285, 9.009649276733398], [7.134974002838135, 9.013984680175781], [7.135768413543701, 9.015036582946777], [7.136971950531006, 9.016631126403809], [7.14292049407959, 9.021770477294922], [7.14727258682251, 9.026894569396973], [7.150493144989014, 9.034317970275879], [7.15461540222168, 9.03897762298584], [7.1556806564331055, 9.041244506835938], [7.156249046325684, 9.04196834564209], [7.1573686599731445, 9.043394088745117], [7.157974720001221, 9.045899391174316], [7.158087253570557, 9.046363830566406], [7.158161163330078, 9.046521186828613], [7.161052703857422, 9.0526704788208], [7.161081314086914, 9.052716255187988], [7.159873962402344, 9.053744316101074], [7.160669326782227, 9.056941986083984], [7.1608710289001465, 9.05775260925293], [7.161630630493164, 9.061182975769043], [7.162140369415283, 9.06192398071289], [7.162614822387695, 9.063135147094727], [7.163594722747803, 9.065635681152344], [7.166009426116943, 9.07559585571289], [7.165672779083252, 9.08506965637207], [7.165372371673584, 9.088432312011719], [7.164676189422607, 9.096222877502441], [7.163971424102783, 9.097650527954102], [7.164468765258789, 9.097776412963867], [7.164672374725342, 9.09782886505127], [7.166687965393066, 9.09834098815918], [7.1702728271484375, 9.099250793457031]]], &quot;type&quot;: &quot;Polygon&quot;}, &quot;id&quot;: &quot;314&quot;, &quot;properties&quot;: {&quot;AREA&quot;: 1585.32, &quot;FULL_NAME&quot;: null, &quot;LATITUDE&quot;: null, &quot;LGA&quot;: &quot;Abuja Municipal&quot;, &quot;LONGITUDE&quot;: null, &quot;PERIMETER&quot;: 206.501, &quot;STATE&quot;: &quot;Abuja&quot;}, &quot;type&quot;: &quot;Feature&quot;}, {&quot;bbox&quot;: [6.781259059906006, 8.490447044372559, 7.165055751800537, 8.920446395874023], &quot;geometry&quot;: {&quot;coordinates&quot;: [[[7.059737205505371, 8.891996383666992], [7.060636043548584, 8.89094066619873], [7.072084903717041, 8.885677337646484], [7.081886291503906, 8.878368377685547], [7.085635185241699, 8.867705345153809], [7.096161842346191, 8.862458229064941], [7.102693557739258, 8.852669715881348], [7.1105637550354, 8.840093612670898], [7.129117488861084, 8.831945419311523], [7.142838954925537, 8.824569702148438], [7.153670310974121, 8.809639930725098], [7.161522388458252, 8.79591178894043], [7.165055751800537, 8.786174774169922], [7.163012981414795, 8.77399730682373], [7.151318073272705, 8.764057159423828], [7.127928256988525, 8.758461952209473], [7.107514381408691, 8.751434326171875], [7.096109867095947, 8.745176315307617], [7.086479187011719, 8.734509468078613], [7.080311298370361, 8.724015235900879], [7.078585147857666, 8.717131614685059], [7.078383922576904, 8.704691886901855], [7.079797267913818, 8.692225456237793], [7.0841450691223145, 8.675792694091797], [7.091855525970459, 8.667596817016602], [7.095608234405518, 8.657164573669434], [7.10019063949585, 8.640957832336426], [7.088930130004883, 8.615111351013184], [7.073507785797119, 8.602930068969727], [7.055578231811523, 8.578348159790039], [7.032318592071533, 8.566530227661133], [7.0108962059021, 8.554220199584961], [7.000153541564941, 8.546107292175293], [6.993974685668945, 8.534921646118164], [6.990808963775635, 8.524605751037598], [6.987635612487793, 8.513830184936523], [6.978269577026367, 8.505232810974121], [6.974002838134766, 8.498162269592285], [6.967479228973389, 8.490447044372559], [6.959771156311035, 8.502551078796387], [6.949065685272217, 8.505997657775879], [6.941641807556152, 8.50838851928711], [6.936943054199219, 8.513771057128906], [6.9271464347839355, 8.524992942810059], [6.922162055969238, 8.52794075012207], [6.909095287322998, 8.53566837310791], [6.897485256195068, 8.541220664978027], [6.892613410949707, 8.543550491333008], [6.87718391418457, 8.545194625854492], [6.861478328704834, 8.544007301330566], [6.854780197143555, 8.543500900268555], [6.839366912841797, 8.547006607055664], [6.808812618255615, 8.553956985473633], [6.802844047546387, 8.556137084960938], [6.7916131019592285, 8.5602388381958], [6.79224967956543, 8.585343360900879], [6.787306785583496, 8.59349250793457], [6.781259059906006, 8.618940353393555], [6.784402847290039, 8.627873420715332], [6.785288333892822, 8.630322456359863], [6.7873005867004395, 8.635889053344727], [6.789302349090576, 8.642961502075195], [6.790481090545654, 8.647126197814941], [6.786464214324951, 8.655488967895508], [6.782435894012451, 8.663161277770996], [6.784560203552246, 8.666703224182129], [6.78599214553833, 8.66909122467041], [6.78761625289917, 8.672038078308105], [6.7902703285217285, 8.67685317993164], [6.794068813323975, 8.69775676727295], [6.793515205383301, 8.72057819366455], [6.788330554962158, 8.742324829101562], [6.789190769195557, 8.76696491241455], [6.794849872589111, 8.779057502746582], [6.796359062194824, 8.782281875610352], [6.806248188018799, 8.789917945861816], [6.806870937347412, 8.790398597717285], [6.816810131072998, 8.799225807189941], [6.82138204574585, 8.8032865524292], [6.8262104988098145, 8.81126880645752], [6.830626010894775, 8.818568229675293], [6.832564353942871, 8.821863174438477], [6.840826034545898, 8.835906982421875], [6.8442206382751465, 8.846237182617188], [6.847377300262451, 8.855842590332031], [6.849334716796875, 8.864961624145508], [6.8513689041137695, 8.874439239501953], [6.850525379180908, 8.893577575683594], [6.85053825378418, 8.903972625732422], [6.850544452667236, 8.909015655517578], [6.852778911590576, 8.912459373474121], [6.854093074798584, 8.914484977722168], [6.854711055755615, 8.914072036743164], [6.854801177978516, 8.91401195526123], [6.861167907714844, 8.909757614135742], [6.873271465301514, 8.902178764343262], [6.884523391723633, 8.898992538452148], [6.905059814453125, 8.899335861206055], [6.9207868576049805, 8.901834487915039], [6.942361354827881, 8.909303665161133], [6.97157621383667, 8.918486595153809], [6.991908073425293, 8.920446395874023], [7.003241539001465, 8.908041954040527], [7.015452861785889, 8.892857551574707], [7.034088611602783, 8.889777183532715], [7.053702354431152, 8.890135765075684], [7.059737205505371, 8.891996383666992]]], &quot;type&quot;: &quot;Polygon&quot;}, &quot;id&quot;: &quot;329&quot;, &quot;properties&quot;: {&quot;AREA&quot;: 1299.111, &quot;FULL_NAME&quot;: null, &quot;LATITUDE&quot;: null, &quot;LGA&quot;: &quot;Kwali&quot;, &quot;LONGITUDE&quot;: null, &quot;PERIMETER&quot;: 151.853, &quot;STATE&quot;: &quot;Abuja&quot;}, &quot;type&quot;: &quot;Feature&quot;}, {&quot;bbox&quot;: [6.967479228973389, 8.410850524902344, 7.500400543212891, 8.900973320007324], &quot;geometry&quot;: {&quot;coordinates&quot;: [[[7.072854995727539, 8.900973320007324], [7.083162784576416, 8.900236129760742], [7.100920677185059, 8.899935722351074], [7.103911399841309, 8.89942455291748], [7.115103721618652, 8.892552375793457], [7.128196716308594, 8.889104843139648], [7.14108943939209, 8.887503623962402], [7.151861190795898, 8.883173942565918], [7.162153720855713, 8.877699851989746], [7.191192626953125, 8.861769676208496], [7.226443767547607, 8.844813346862793], [7.272872447967529, 8.820063591003418], [7.31696891784668, 8.808025360107422], [7.341124534606934, 8.803930282592773], [7.358220100402832, 8.8054838180542], [7.373965740203857, 8.809134483337402], [7.390507221221924, 8.804936408996582], [7.413561820983887, 8.804085731506348], [7.427894115447998, 8.805916786193848], [7.44106912612915, 8.807536125183105], [7.449341773986816, 8.805553436279297], [7.452775001525879, 8.803881645202637], [7.456523895263672, 8.793219566345215], [7.453982830047607, 8.778745651245117], [7.457765579223633, 8.770156860351562], [7.462266445159912, 8.763167381286621], [7.475329875946045, 8.757877349853516], [7.486804962158203, 8.754226684570312], [7.4935078620910645, 8.755034446716309], [7.497640132904053, 8.753812789916992], [7.500400543212891, 8.753305435180664], [7.500143527984619, 8.751696586608887], [7.497740745544434, 8.745746612548828], [7.495515823364258, 8.736567497253418], [7.491914749145508, 8.727871894836426], [7.488287925720215, 8.717564582824707], [7.484251976013184, 8.710490226745605], [7.482577800750732, 8.706831932067871], [7.480855464935303, 8.700179100036621], [7.474259853363037, 8.691764831542969], [7.464439868927002, 8.683636665344238], [7.4550957679748535, 8.676421165466309], [7.445223331451416, 8.665067672729492], [7.435640811920166, 8.65739631652832], [7.423484802246094, 8.64746379852295], [7.415807247161865, 8.64344596862793], [7.411786079406738, 8.637292861938477], [7.415352821350098, 8.62962818145752], [7.425001621246338, 8.627161026000977], [7.4295806884765625, 8.625009536743164], [7.43723201751709, 8.62741470336914], [7.442543983459473, 8.627785682678223], [7.449700355529785, 8.628125190734863], [7.457813262939453, 8.630521774291992], [7.464780330657959, 8.633399963378906], [7.468782901763916, 8.63840103149414], [7.471071243286133, 8.639214515686035], [7.469948768615723, 8.635472297668457], [7.463099479675293, 8.625899314880371], [7.462868690490723, 8.625903129577637], [7.45313024520874, 8.608997344970703], [7.4374098777771, 8.593347549438477], [7.428438186645508, 8.581042289733887], [7.419459342956543, 8.568276405334473], [7.408596992492676, 8.553235054016113], [7.402894020080566, 8.543181419372559], [7.392138957977295, 8.534829139709473], [7.373283386230469, 8.525230407714844], [7.356590747833252, 8.52090072631836], [7.3425822257995605, 8.511219024658203], [7.3304643630981445, 8.504274368286133], [7.315785884857178, 8.495988845825195], [7.30111837387085, 8.488394737243652], [7.287415027618408, 8.483322143554688], [7.2796831130981445, 8.476302146911621], [7.263444423675537, 8.471504211425781], [7.238888740539551, 8.466385841369629], [7.218049049377441, 8.4625883102417], [7.204833030700684, 8.459122657775879], [7.189253330230713, 8.45223617553711], [7.179935455322266, 8.447089195251465], [7.157885551452637, 8.439851760864258], [7.131509780883789, 8.436379432678223], [7.112527370452881, 8.433242797851562], [7.091244220733643, 8.4306058883667], [7.063658237457275, 8.423693656921387], [7.036169528961182, 8.422778129577637], [7.021378517150879, 8.421876907348633], [7.007184028625488, 8.41496753692627], [6.983361721038818, 8.412374496459961], [6.978153705596924, 8.411813735961914], [6.969207286834717, 8.410850524902344], [6.975081920623779, 8.430357933044434], [6.976682662963867, 8.435673713684082], [6.978899955749512, 8.444392204284668], [6.98387336730957, 8.452372550964355], [6.984955787658691, 8.471471786499023], [6.985164642333984, 8.4751615524292], [6.983405590057373, 8.480491638183594], [6.973269462585449, 8.481354713439941], [6.972510814666748, 8.482545852661133], [6.967479228973389, 8.490447044372559], [6.974002838134766, 8.498162269592285], [6.978269577026367, 8.505232810974121], [6.987635612487793, 8.513830184936523], [6.990808963775635, 8.524605751037598], [6.993974685668945, 8.534921646118164], [7.000153541564941, 8.546107292175293], [7.0108962059021, 8.554220199584961], [7.032318592071533, 8.566530227661133], [7.055578231811523, 8.578348159790039], [7.073507785797119, 8.602930068969727], [7.088930130004883, 8.615111351013184], [7.10019063949585, 8.640957832336426], [7.095608234405518, 8.657164573669434], [7.091855525970459, 8.667596817016602], [7.0841450691223145, 8.675792694091797], [7.079797267913818, 8.692225456237793], [7.078383922576904, 8.704691886901855], [7.078585147857666, 8.717131614685059], [7.080311298370361, 8.724015235900879], [7.086479187011719, 8.734509468078613], [7.096109867095947, 8.745176315307617], [7.107514381408691, 8.751434326171875], [7.127928256988525, 8.758461952209473], [7.151318073272705, 8.764057159423828], [7.163012981414795, 8.77399730682373], [7.165055751800537, 8.786174774169922], [7.161522388458252, 8.79591178894043], [7.153670310974121, 8.809639930725098], [7.142838954925537, 8.824569702148438], [7.129117488861084, 8.831945419311523], [7.1105637550354, 8.840093612670898], [7.102693557739258, 8.852669715881348], [7.096161842346191, 8.862458229064941], [7.085635185241699, 8.867705345153809], [7.081886291503906, 8.878368377685547], [7.072084903717041, 8.885677337646484], [7.060636043548584, 8.89094066619873], [7.059737205505371, 8.891996383666992], [7.066453456878662, 8.894067764282227], [7.071371078491211, 8.898592948913574], [7.072434902191162, 8.900299072265625], [7.072854995727539, 8.900973320007324]]], &quot;type&quot;: &quot;Polygon&quot;}, &quot;id&quot;: &quot;331&quot;, &quot;properties&quot;: {&quot;AREA&quot;: 1708.466, &quot;FULL_NAME&quot;: null, &quot;LATITUDE&quot;: null, &quot;LGA&quot;: &quot;Kuje&quot;, &quot;LONGITUDE&quot;: null, &quot;PERIMETER&quot;: 217.281, &quot;STATE&quot;: &quot;Abuja&quot;}, &quot;type&quot;: &quot;Feature&quot;}], &quot;type&quot;: &quot;FeatureCollection&quot;});



    geo_json_35a3489f5976a6d77cc9471066ff93e3.bindTooltip(
    function(layer){
    let div = L.DomUtil.create(&#x27;div&#x27;);

    let handleObject = feature=&gt;typeof(feature)==&#x27;object&#x27; ? JSON.stringify(feature) : feature;
    let fields = [&quot;STATE&quot;, &quot;LGA&quot;, &quot;AREA&quot;, &quot;PERIMETER&quot;, &quot;LONGITUDE&quot;, &quot;LATITUDE&quot;, &quot;FULL_NAME&quot;];
    let aliases = [&quot;STATE&quot;, &quot;LGA&quot;, &quot;AREA&quot;, &quot;PERIMETER&quot;, &quot;LONGITUDE&quot;, &quot;LATITUDE&quot;, &quot;FULL_NAME&quot;];
    let table = &#x27;&lt;table&gt;&#x27; +
        String(
        fields.map(
        (v,i)=&gt;
        `&lt;tr&gt;
            &lt;th&gt;${aliases[i]}&lt;/th&gt;

            &lt;td&gt;${handleObject(layer.feature.properties[v])}&lt;/td&gt;
        &lt;/tr&gt;`).join(&#x27;&#x27;))
    +&#x27;&lt;/table&gt;&#x27;;
    div.innerHTML=table;

    return div
    }
    ,{&quot;className&quot;: &quot;foliumtooltip&quot;, &quot;sticky&quot;: true});

&lt;/script&gt;
&lt;/html&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>




```python
# Select Gwagwalada
Gwagwalada = Abuja[Abuja["LGA"] == 'Gwagwalada']
Gwagwalada
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>STATE</th>
      <th>LGA</th>
      <th>AREA</th>
      <th>PERIMETER</th>
      <th>LONGITUDE</th>
      <th>LATITUDE</th>
      <th>FULL_NAME</th>
      <th>geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>307</th>
      <td>Abuja</td>
      <td>Gwagwalada</td>
      <td>967.762</td>
      <td>129.429</td>
      <td>None</td>
      <td>None</td>
      <td>None</td>
      <td>POLYGON ((7.12020 9.12689, 7.12679 9.11870, 7....</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Display Gwagwalada Map
Gwagwalada.explore()
```




<div style="width:100%;"><div style="position:relative;width:100%;height:0;padding-bottom:60%;"><span style="color:#565656">Make this Notebook Trusted to load map: File -> Trust Notebook</span><iframe srcdoc="&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;

    &lt;meta http-equiv=&quot;content-type&quot; content=&quot;text/html; charset=UTF-8&quot; /&gt;

        &lt;script&gt;
            L_NO_TOUCH = false;
            L_DISABLE_3D = false;
        &lt;/script&gt;

    &lt;style&gt;html, body {width: 100%;height: 100%;margin: 0;padding: 0;}&lt;/style&gt;
    &lt;style&gt;#map {position:absolute;top:0;bottom:0;right:0;left:0;}&lt;/style&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://code.jquery.com/jquery-1.12.4.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/js/bootstrap.bundle.min.js&quot;&gt;&lt;/script&gt;
    &lt;script src=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.js&quot;&gt;&lt;/script&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/leaflet@1.9.3/dist/leaflet.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/bootstrap@5.2.2/dist/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://netdna.bootstrapcdn.com/bootstrap/3.0.0/css/bootstrap.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/npm/@fortawesome/fontawesome-free@6.2.0/css/all.min.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdnjs.cloudflare.com/ajax/libs/Leaflet.awesome-markers/2.0.2/leaflet.awesome-markers.css&quot;/&gt;
    &lt;link rel=&quot;stylesheet&quot; href=&quot;https://cdn.jsdelivr.net/gh/python-visualization/folium/folium/templates/leaflet.awesome.rotate.min.css&quot;/&gt;

            &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width,
                initial-scale=1.0, maximum-scale=1.0, user-scalable=no&quot; /&gt;
            &lt;style&gt;
                #map_c30218f1c815008f8933584fa06ef9d9 {
                    position: relative;
                    width: 100.0%;
                    height: 100.0%;
                    left: 0.0%;
                    top: 0.0%;
                }
                .leaflet-container { font-size: 1rem; }
            &lt;/style&gt;


                    &lt;style&gt;
                        .foliumtooltip {

                        }
                       .foliumtooltip table{
                            margin: auto;
                        }
                        .foliumtooltip tr{
                            text-align: left;
                        }
                        .foliumtooltip th{
                            padding: 2px; padding-right: 8px;
                        }
                    &lt;/style&gt;

&lt;/head&gt;
&lt;body&gt;


            &lt;div class=&quot;folium-map&quot; id=&quot;map_c30218f1c815008f8933584fa06ef9d9&quot; &gt;&lt;/div&gt;

&lt;/body&gt;
&lt;script&gt;


            var map_c30218f1c815008f8933584fa06ef9d9 = L.map(
                &quot;map_c30218f1c815008f8933584fa06ef9d9&quot;,
                {
                    center: [9.062745094299316, 6.9945783615112305],
                    crs: L.CRS.EPSG3857,
                    zoom: 10,
                    zoomControl: true,
                    preferCanvas: false,
                }
            );
            L.control.scale().addTo(map_c30218f1c815008f8933584fa06ef9d9);





            var tile_layer_303546b7952cddc87100005c875d1af2 = L.tileLayer(
                &quot;https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png&quot;,
                {&quot;attribution&quot;: &quot;Data by \u0026copy; \u003ca target=\&quot;_blank\&quot; href=\&quot;http://openstreetmap.org\&quot;\u003eOpenStreetMap\u003c/a\u003e, under \u003ca target=\&quot;_blank\&quot; href=\&quot;http://www.openstreetmap.org/copyright\&quot;\u003eODbL\u003c/a\u003e.&quot;, &quot;detectRetina&quot;: false, &quot;maxNativeZoom&quot;: 18, &quot;maxZoom&quot;: 18, &quot;minZoom&quot;: 0, &quot;noWrap&quot;: false, &quot;opacity&quot;: 1, &quot;subdomains&quot;: &quot;abc&quot;, &quot;tms&quot;: false}
            ).addTo(map_c30218f1c815008f8933584fa06ef9d9);


            map_c30218f1c815008f8933584fa06ef9d9.fitBounds(
                [[8.889777183532715, 6.823147296905518], [9.235713005065918, 7.166009426116943]],
                {}
            );


        function geo_json_9b51a20e563c7c09557b8aef2accd3d4_styler(feature) {
            switch(feature.id) {
                default:
                    return {&quot;fillOpacity&quot;: 0.5, &quot;weight&quot;: 2};
            }
        }
        function geo_json_9b51a20e563c7c09557b8aef2accd3d4_highlighter(feature) {
            switch(feature.id) {
                default:
                    return {&quot;fillOpacity&quot;: 0.75};
            }
        }
        function geo_json_9b51a20e563c7c09557b8aef2accd3d4_pointToLayer(feature, latlng) {
            var opts = {&quot;bubblingMouseEvents&quot;: true, &quot;color&quot;: &quot;#3388ff&quot;, &quot;dashArray&quot;: null, &quot;dashOffset&quot;: null, &quot;fill&quot;: true, &quot;fillColor&quot;: &quot;#3388ff&quot;, &quot;fillOpacity&quot;: 0.2, &quot;fillRule&quot;: &quot;evenodd&quot;, &quot;lineCap&quot;: &quot;round&quot;, &quot;lineJoin&quot;: &quot;round&quot;, &quot;opacity&quot;: 1.0, &quot;radius&quot;: 2, &quot;stroke&quot;: true, &quot;weight&quot;: 3};

            let style = geo_json_9b51a20e563c7c09557b8aef2accd3d4_styler(feature)
            Object.assign(opts, style)

            return new L.CircleMarker(latlng, opts)
        }

        function geo_json_9b51a20e563c7c09557b8aef2accd3d4_onEachFeature(feature, layer) {
            layer.on({
                mouseout: function(e) {
                    if(typeof e.target.setStyle === &quot;function&quot;){
                        geo_json_9b51a20e563c7c09557b8aef2accd3d4.resetStyle(e.target);
                    }
                },
                mouseover: function(e) {
                    if(typeof e.target.setStyle === &quot;function&quot;){
                        const highlightStyle = geo_json_9b51a20e563c7c09557b8aef2accd3d4_highlighter(e.target.feature)
                        e.target.setStyle(highlightStyle);
                    }
                },
            });
        };
        var geo_json_9b51a20e563c7c09557b8aef2accd3d4 = L.geoJson(null, {
                onEachFeature: geo_json_9b51a20e563c7c09557b8aef2accd3d4_onEachFeature,

                style: geo_json_9b51a20e563c7c09557b8aef2accd3d4_styler,
                pointToLayer: geo_json_9b51a20e563c7c09557b8aef2accd3d4_pointToLayer
        });

        function geo_json_9b51a20e563c7c09557b8aef2accd3d4_add (data) {
            geo_json_9b51a20e563c7c09557b8aef2accd3d4
                .addData(data)
                .addTo(map_c30218f1c815008f8933584fa06ef9d9);
        }
            geo_json_9b51a20e563c7c09557b8aef2accd3d4_add({&quot;bbox&quot;: [6.823147296905518, 8.889777183532715, 7.166009426116943, 9.235713005065918], &quot;features&quot;: [{&quot;bbox&quot;: [6.823147296905518, 8.889777183532715, 7.166009426116943, 9.235713005065918], &quot;geometry&quot;: {&quot;coordinates&quot;: [[[7.120201110839844, 9.126890182495117], [7.1267876625061035, 9.118703842163086], [7.137685298919678, 9.106780052185059], [7.145867347717285, 9.098994255065918], [7.1559600830078125, 9.095616340637207], [7.163837432861328, 9.097616195678711], [7.1639018058776855, 9.09763240814209], [7.163971424102783, 9.097650527954102], [7.164676189422607, 9.096222877502441], [7.165372371673584, 9.088432312011719], [7.165672779083252, 9.08506965637207], [7.166009426116943, 9.07559585571289], [7.163594722747803, 9.065635681152344], [7.162614822387695, 9.063135147094727], [7.162140369415283, 9.06192398071289], [7.161630630493164, 9.061182975769043], [7.1608710289001465, 9.05775260925293], [7.160669326782227, 9.056941986083984], [7.159873962402344, 9.053744316101074], [7.161081314086914, 9.052716255187988], [7.161052703857422, 9.0526704788208], [7.158161163330078, 9.046521186828613], [7.158087253570557, 9.046363830566406], [7.157974720001221, 9.045899391174316], [7.1573686599731445, 9.043394088745117], [7.156249046325684, 9.04196834564209], [7.1556806564331055, 9.041244506835938], [7.15461540222168, 9.03897762298584], [7.150493144989014, 9.034317970275879], [7.14727258682251, 9.026894569396973], [7.14292049407959, 9.021770477294922], [7.136971950531006, 9.016631126403809], [7.135768413543701, 9.015036582946777], [7.134974002838135, 9.013984680175781], [7.1317009925842285, 9.009649276733398], [7.130025863647461, 9.007782936096191], [7.128356456756592, 9.005922317504883], [7.127404689788818, 9.004600524902344], [7.127126216888428, 9.0042142868042], [7.1270246505737305, 9.004073143005371], [7.126990795135498, 9.004026412963867], [7.125895023345947, 9.0025053024292], [7.123831272125244, 8.999641418457031], [7.123340129852295, 8.998787879943848], [7.123060703277588, 8.998302459716797], [7.122759819030762, 8.997779846191406], [7.124849796295166, 8.99777603149414], [7.124354362487793, 8.99704647064209], [7.124336242675781, 8.99701976776123], [7.124171733856201, 8.99677848815918], [7.117486953735352, 8.98283576965332], [7.10786771774292, 8.972860336303711], [7.097280979156494, 8.960136413574219], [7.090384483337402, 8.947349548339844], [7.080552577972412, 8.938529968261719], [7.072328090667725, 8.929222106933594], [7.066224098205566, 8.922643661499023], [7.06335973739624, 8.916701316833496], [7.060952663421631, 8.910520553588867], [7.070489883422852, 8.901142120361328], [7.071489334106445, 8.901070594787598], [7.072854995727539, 8.900973320007324], [7.072434902191162, 8.900299072265625], [7.071371078491211, 8.898592948913574], [7.066453456878662, 8.894067764282227], [7.059737205505371, 8.891996383666992], [7.053702354431152, 8.890135765075684], [7.034088611602783, 8.889777183532715], [7.015452861785889, 8.892857551574707], [7.003241539001465, 8.908041954040527], [6.991908073425293, 8.920446395874023], [6.97157621383667, 8.918486595153809], [6.942361354827881, 8.909303665161133], [6.9207868576049805, 8.901834487915039], [6.905059814453125, 8.899335861206055], [6.884523391723633, 8.898992538452148], [6.873271465301514, 8.902178764343262], [6.861167907714844, 8.909757614135742], [6.854801177978516, 8.91401195526123], [6.8549323081970215, 8.914518356323242], [6.855514049530029, 8.916765213012695], [6.855867862701416, 8.924363136291504], [6.851329803466797, 8.929048538208008], [6.85132360458374, 8.936899185180664], [6.851318836212158, 8.942643165588379], [6.848492622375488, 8.953290939331055], [6.84729528427124, 8.964831352233887], [6.847487449645996, 8.967083930969238], [6.848433494567871, 8.978177070617676], [6.849812030792236, 8.986673355102539], [6.8504838943481445, 8.990815162658691], [6.850449562072754, 8.99280071258545], [6.850348949432373, 8.998648643493652], [6.854900360107422, 8.998701095581055], [6.854478359222412, 9.000038146972656], [6.852832317352295, 9.00401496887207], [6.851289749145508, 9.007742881774902], [6.850897312164307, 9.00869083404541], [6.842442512512207, 9.023658752441406], [6.838243007659912, 9.034355163574219], [6.840795516967773, 9.044961929321289], [6.844248294830322, 9.053750991821289], [6.848376274108887, 9.058653831481934], [6.855942726135254, 9.070740699768066], [6.854608058929443, 9.080119132995605], [6.850751876831055, 9.087152481079102], [6.846986293792725, 9.090287208557129], [6.837571620941162, 9.095599174499512], [6.829987049102783, 9.10049057006836], [6.823147296905518, 9.109526634216309], [6.828204154968262, 9.115825653076172], [6.827600479125977, 9.124532699584961], [6.829379558563232, 9.13056468963623], [6.834251880645752, 9.135224342346191], [6.841784954071045, 9.138818740844727], [6.851562023162842, 9.141544342041016], [6.859800815582275, 9.144696235656738], [6.8627214431762695, 9.14980411529541], [6.867778301239014, 9.156103134155273], [6.868140697479248, 9.164140701293945], [6.8709282875061035, 9.168327331542969], [6.877030849456787, 9.170289039611816], [6.894341468811035, 9.171792984008789], [6.908311367034912, 9.17735767364502], [6.914866924285889, 9.18345832824707], [6.916130542755127, 9.189678192138672], [6.918971538543701, 9.195243835449219], [6.91889762878418, 9.205795288085938], [6.921220779418945, 9.209284782409668], [6.933000564575195, 9.212281227111816], [6.942488670349121, 9.220047950744629], [6.950276851654053, 9.224108695983887], [6.961634635925293, 9.231225967407227], [6.963187217712402, 9.232404708862305], [6.979012966156006, 9.235713005065918], [6.991518974304199, 9.23115348815918], [7.007513523101807, 9.225592613220215], [7.016153335571289, 9.214573860168457], [7.036712169647217, 9.1990385055542], [7.0495710372924805, 9.188519477844238], [7.063132286071777, 9.179393768310547], [7.073759078979492, 9.166312217712402], [7.090111255645752, 9.150507926940918], [7.104808330535889, 9.139081001281738], [7.117740631103516, 9.129949569702148], [7.120201110839844, 9.126890182495117]]], &quot;type&quot;: &quot;Polygon&quot;}, &quot;id&quot;: &quot;307&quot;, &quot;properties&quot;: {&quot;AREA&quot;: 967.762, &quot;FULL_NAME&quot;: null, &quot;LATITUDE&quot;: null, &quot;LGA&quot;: &quot;Gwagwalada&quot;, &quot;LONGITUDE&quot;: null, &quot;PERIMETER&quot;: 129.429, &quot;STATE&quot;: &quot;Abuja&quot;}, &quot;type&quot;: &quot;Feature&quot;}], &quot;type&quot;: &quot;FeatureCollection&quot;});



    geo_json_9b51a20e563c7c09557b8aef2accd3d4.bindTooltip(
    function(layer){
    let div = L.DomUtil.create(&#x27;div&#x27;);

    let handleObject = feature=&gt;typeof(feature)==&#x27;object&#x27; ? JSON.stringify(feature) : feature;
    let fields = [&quot;STATE&quot;, &quot;LGA&quot;, &quot;AREA&quot;, &quot;PERIMETER&quot;, &quot;LONGITUDE&quot;, &quot;LATITUDE&quot;, &quot;FULL_NAME&quot;];
    let aliases = [&quot;STATE&quot;, &quot;LGA&quot;, &quot;AREA&quot;, &quot;PERIMETER&quot;, &quot;LONGITUDE&quot;, &quot;LATITUDE&quot;, &quot;FULL_NAME&quot;];
    let table = &#x27;&lt;table&gt;&#x27; +
        String(
        fields.map(
        (v,i)=&gt;
        `&lt;tr&gt;
            &lt;th&gt;${aliases[i]}&lt;/th&gt;

            &lt;td&gt;${handleObject(layer.feature.properties[v])}&lt;/td&gt;
        &lt;/tr&gt;`).join(&#x27;&#x27;))
    +&#x27;&lt;/table&gt;&#x27;;
    div.innerHTML=table;

    return div
    }
    ,{&quot;className&quot;: &quot;foliumtooltip&quot;, &quot;sticky&quot;: true});

&lt;/script&gt;
&lt;/html&gt;" style="position:absolute;width:100%;height:100%;left:0;top:0;border:none !important;" allowfullscreen webkitallowfullscreen mozallowfullscreen></iframe></div></div>




```python
# Initialize AppeearsDownloader for MODIS NDVI data
ndvi_downloader = eaapp.AppeearsDownloader(
    download_key='abuja-ndvi',
    ea_dir=abuja_dir,
    product='MOD13Q1.061',
    layer='_250m_16_days_NDVI',
    start_date="07-01",
    end_date="07-31",
    recurring=True,
    year_range=[2015, 2020],
    polygon= Gwagwalada
)

ndvi_downloader.download_files(cache=True)
```


```python
# Get a list of NDVI tif file paths
abuja_paths = sorted(glob(os.path.join(abuja_dir, 'abuja-ndvi', '*', '*NDVI*.tif')))
len(abuja_paths)
```




    18




```python
scale_factor = 10000
doy_start = -19
doy_end = -12
```


```python
abuja_das = []
for abuja_path in abuja_paths:
    # Get date from file name
    doy = abuja_path [doy_start:doy_end]
    date = pd.to_datetime(doy, format='%Y%j')

    # Open dataset
    da = rxr.open_rasterio(abuja_path, masked=True).squeeze()

    # Add date dimension and clean up metadata
    da = da.assign_coords({'date': date})
    da = da.expand_dims({'date': 1})
    da.name = 'NDVI'

    # Multiple by scale factor
    da = da / scale_factor

    # Prepare for concatenation
    abuja_das.append(da)

len(abuja_das)
```




    18




```python
abuja_da = xr.combine_by_coords(abuja_das, coords=['date'])
abuja_da
```




<div><svg style="position: absolute; width: 0; height: 0; overflow: hidden">
<defs>
<symbol id="icon-database" viewBox="0 0 32 32">
<path d="M16 0c-8.837 0-16 2.239-16 5v4c0 2.761 7.163 5 16 5s16-2.239 16-5v-4c0-2.761-7.163-5-16-5z"></path>
<path d="M16 17c-8.837 0-16-2.239-16-5v6c0 2.761 7.163 5 16 5s16-2.239 16-5v-6c0 2.761-7.163 5-16 5z"></path>
<path d="M16 26c-8.837 0-16-2.239-16-5v6c0 2.761 7.163 5 16 5s16-2.239 16-5v-6c0 2.761-7.163 5-16 5z"></path>
</symbol>
<symbol id="icon-file-text2" viewBox="0 0 32 32">
<path d="M28.681 7.159c-0.694-0.947-1.662-2.053-2.724-3.116s-2.169-2.030-3.116-2.724c-1.612-1.182-2.393-1.319-2.841-1.319h-15.5c-1.378 0-2.5 1.121-2.5 2.5v27c0 1.378 1.122 2.5 2.5 2.5h23c1.378 0 2.5-1.122 2.5-2.5v-19.5c0-0.448-0.137-1.23-1.319-2.841zM24.543 5.457c0.959 0.959 1.712 1.825 2.268 2.543h-4.811v-4.811c0.718 0.556 1.584 1.309 2.543 2.268zM28 29.5c0 0.271-0.229 0.5-0.5 0.5h-23c-0.271 0-0.5-0.229-0.5-0.5v-27c0-0.271 0.229-0.5 0.5-0.5 0 0 15.499-0 15.5 0v7c0 0.552 0.448 1 1 1h7v19.5z"></path>
<path d="M23 26h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
<path d="M23 22h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
<path d="M23 18h-14c-0.552 0-1-0.448-1-1s0.448-1 1-1h14c0.552 0 1 0.448 1 1s-0.448 1-1 1z"></path>
</symbol>
</defs>
</svg>
<style>/* CSS stylesheet for displaying xarray objects in jupyterlab.
 *
 */

:root {
  --xr-font-color0: var(--jp-content-font-color0, rgba(0, 0, 0, 1));
  --xr-font-color2: var(--jp-content-font-color2, rgba(0, 0, 0, 0.54));
  --xr-font-color3: var(--jp-content-font-color3, rgba(0, 0, 0, 0.38));
  --xr-border-color: var(--jp-border-color2, #e0e0e0);
  --xr-disabled-color: var(--jp-layout-color3, #bdbdbd);
  --xr-background-color: var(--jp-layout-color0, white);
  --xr-background-color-row-even: var(--jp-layout-color1, white);
  --xr-background-color-row-odd: var(--jp-layout-color2, #eeeeee);
}

html[theme=dark],
body[data-theme=dark],
body.vscode-dark {
  --xr-font-color0: rgba(255, 255, 255, 1);
  --xr-font-color2: rgba(255, 255, 255, 0.54);
  --xr-font-color3: rgba(255, 255, 255, 0.38);
  --xr-border-color: #1F1F1F;
  --xr-disabled-color: #515151;
  --xr-background-color: #111111;
  --xr-background-color-row-even: #111111;
  --xr-background-color-row-odd: #313131;
}

.xr-wrap {
  display: block !important;
  min-width: 300px;
  max-width: 700px;
}

.xr-text-repr-fallback {
  /* fallback to plain text repr when CSS is not injected (untrusted notebook) */
  display: none;
}

.xr-header {
  padding-top: 6px;
  padding-bottom: 6px;
  margin-bottom: 4px;
  border-bottom: solid 1px var(--xr-border-color);
}

.xr-header > div,
.xr-header > ul {
  display: inline;
  margin-top: 0;
  margin-bottom: 0;
}

.xr-obj-type,
.xr-array-name {
  margin-left: 2px;
  margin-right: 10px;
}

.xr-obj-type {
  color: var(--xr-font-color2);
}

.xr-sections {
  padding-left: 0 !important;
  display: grid;
  grid-template-columns: 150px auto auto 1fr 20px 20px;
}

.xr-section-item {
  display: contents;
}

.xr-section-item input {
  display: none;
}

.xr-section-item input + label {
  color: var(--xr-disabled-color);
}

.xr-section-item input:enabled + label {
  cursor: pointer;
  color: var(--xr-font-color2);
}

.xr-section-item input:enabled + label:hover {
  color: var(--xr-font-color0);
}

.xr-section-summary {
  grid-column: 1;
  color: var(--xr-font-color2);
  font-weight: 500;
}

.xr-section-summary > span {
  display: inline-block;
  padding-left: 0.5em;
}

.xr-section-summary-in:disabled + label {
  color: var(--xr-font-color2);
}

.xr-section-summary-in + label:before {
  display: inline-block;
  content: '►';
  font-size: 11px;
  width: 15px;
  text-align: center;
}

.xr-section-summary-in:disabled + label:before {
  color: var(--xr-disabled-color);
}

.xr-section-summary-in:checked + label:before {
  content: '▼';
}

.xr-section-summary-in:checked + label > span {
  display: none;
}

.xr-section-summary,
.xr-section-inline-details {
  padding-top: 4px;
  padding-bottom: 4px;
}

.xr-section-inline-details {
  grid-column: 2 / -1;
}

.xr-section-details {
  display: none;
  grid-column: 1 / -1;
  margin-bottom: 5px;
}

.xr-section-summary-in:checked ~ .xr-section-details {
  display: contents;
}

.xr-array-wrap {
  grid-column: 1 / -1;
  display: grid;
  grid-template-columns: 20px auto;
}

.xr-array-wrap > label {
  grid-column: 1;
  vertical-align: top;
}

.xr-preview {
  color: var(--xr-font-color3);
}

.xr-array-preview,
.xr-array-data {
  padding: 0 5px !important;
  grid-column: 2;
}

.xr-array-data,
.xr-array-in:checked ~ .xr-array-preview {
  display: none;
}

.xr-array-in:checked ~ .xr-array-data,
.xr-array-preview {
  display: inline-block;
}

.xr-dim-list {
  display: inline-block !important;
  list-style: none;
  padding: 0 !important;
  margin: 0;
}

.xr-dim-list li {
  display: inline-block;
  padding: 0;
  margin: 0;
}

.xr-dim-list:before {
  content: '(';
}

.xr-dim-list:after {
  content: ')';
}

.xr-dim-list li:not(:last-child):after {
  content: ',';
  padding-right: 5px;
}

.xr-has-index {
  font-weight: bold;
}

.xr-var-list,
.xr-var-item {
  display: contents;
}

.xr-var-item > div,
.xr-var-item label,
.xr-var-item > .xr-var-name span {
  background-color: var(--xr-background-color-row-even);
  margin-bottom: 0;
}

.xr-var-item > .xr-var-name:hover span {
  padding-right: 5px;
}

.xr-var-list > li:nth-child(odd) > div,
.xr-var-list > li:nth-child(odd) > label,
.xr-var-list > li:nth-child(odd) > .xr-var-name span {
  background-color: var(--xr-background-color-row-odd);
}

.xr-var-name {
  grid-column: 1;
}

.xr-var-dims {
  grid-column: 2;
}

.xr-var-dtype {
  grid-column: 3;
  text-align: right;
  color: var(--xr-font-color2);
}

.xr-var-preview {
  grid-column: 4;
}

.xr-index-preview {
  grid-column: 2 / 5;
  color: var(--xr-font-color2);
}

.xr-var-name,
.xr-var-dims,
.xr-var-dtype,
.xr-preview,
.xr-attrs dt {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  padding-right: 10px;
}

.xr-var-name:hover,
.xr-var-dims:hover,
.xr-var-dtype:hover,
.xr-attrs dt:hover {
  overflow: visible;
  width: auto;
  z-index: 1;
}

.xr-var-attrs,
.xr-var-data,
.xr-index-data {
  display: none;
  background-color: var(--xr-background-color) !important;
  padding-bottom: 5px !important;
}

.xr-var-attrs-in:checked ~ .xr-var-attrs,
.xr-var-data-in:checked ~ .xr-var-data,
.xr-index-data-in:checked ~ .xr-index-data {
  display: block;
}

.xr-var-data > table {
  float: right;
}

.xr-var-name span,
.xr-var-data,
.xr-index-name div,
.xr-index-data,
.xr-attrs {
  padding-left: 25px !important;
}

.xr-attrs,
.xr-var-attrs,
.xr-var-data,
.xr-index-data {
  grid-column: 1 / -1;
}

dl.xr-attrs {
  padding: 0;
  margin: 0;
  display: grid;
  grid-template-columns: 125px auto;
}

.xr-attrs dt,
.xr-attrs dd {
  padding: 0;
  margin: 0;
  float: left;
  padding-right: 10px;
  width: auto;
}

.xr-attrs dt {
  font-weight: normal;
  grid-column: 1;
}

.xr-attrs dt:hover span {
  display: inline-block;
  background: var(--xr-background-color);
  padding-right: 10px;
}

.xr-attrs dd {
  grid-column: 2;
  white-space: pre-wrap;
  word-break: break-all;
}

.xr-icon-database,
.xr-icon-file-text2,
.xr-no-icon {
  display: inline-block;
  vertical-align: middle;
  width: 1em;
  height: 1.5em !important;
  stroke-width: 0;
  stroke: currentColor;
  fill: currentColor;
}
</style><pre class='xr-text-repr-fallback'>&lt;xarray.Dataset&gt;
Dimensions:      (x: 165, y: 167, date: 18)
Coordinates:
    band         int64 1
  * x            (x) float64 6.824 6.826 6.828 6.83 ... 7.159 7.161 7.164 7.166
  * y            (y) float64 9.236 9.234 9.232 9.23 ... 8.897 8.895 8.893 8.891
    spatial_ref  int64 0
  * date         (date) datetime64[ns] 2015-06-26 2015-07-12 ... 2020-07-27
Data variables:
    NDVI         (date, y, x) float32 0.5796 0.5933 0.5727 ... 0.6463 0.6531</pre><div class='xr-wrap' style='display:none'><div class='xr-header'><div class='xr-obj-type'>xarray.Dataset</div></div><ul class='xr-sections'><li class='xr-section-item'><input id='section-b159c22f-3e75-4db5-970c-53587a5f8469' class='xr-section-summary-in' type='checkbox' disabled ><label for='section-b159c22f-3e75-4db5-970c-53587a5f8469' class='xr-section-summary'  title='Expand/collapse section'>Dimensions:</label><div class='xr-section-inline-details'><ul class='xr-dim-list'><li><span class='xr-has-index'>x</span>: 165</li><li><span class='xr-has-index'>y</span>: 167</li><li><span class='xr-has-index'>date</span>: 18</li></ul></div><div class='xr-section-details'></div></li><li class='xr-section-item'><input id='section-14a1262a-ed5c-4092-bde8-d3c3d03c017f' class='xr-section-summary-in' type='checkbox'  checked><label for='section-14a1262a-ed5c-4092-bde8-d3c3d03c017f' class='xr-section-summary' >Coordinates: <span>(5)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-var-name'><span>band</span></div><div class='xr-var-dims'>()</div><div class='xr-var-dtype'>int64</div><div class='xr-var-preview xr-preview'>1</div><input id='attrs-b2ab0e1c-cfab-4347-ac6d-3f8c116d1b5d' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-b2ab0e1c-cfab-4347-ac6d-3f8c116d1b5d' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-751789a3-9d1f-4487-b6f1-e47366b0ca86' class='xr-var-data-in' type='checkbox'><label for='data-751789a3-9d1f-4487-b6f1-e47366b0ca86' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array(1)</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>x</span></div><div class='xr-var-dims'>(x)</div><div class='xr-var-dtype'>float64</div><div class='xr-var-preview xr-preview'>6.824 6.826 6.828 ... 7.164 7.166</div><input id='attrs-696c64c2-5613-4f59-b57e-70638c2f4caa' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-696c64c2-5613-4f59-b57e-70638c2f4caa' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-077edefd-1ce9-40f4-9650-59bedc8bc81b' class='xr-var-data-in' type='checkbox'><label for='data-077edefd-1ce9-40f4-9650-59bedc8bc81b' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([6.823958, 6.826042, 6.828125, 6.830208, 6.832292, 6.834375, 6.836458,
       6.838542, 6.840625, 6.842708, 6.844792, 6.846875, 6.848958, 6.851042,
       6.853125, 6.855208, 6.857292, 6.859375, 6.861458, 6.863542, 6.865625,
       6.867708, 6.869792, 6.871875, 6.873958, 6.876042, 6.878125, 6.880208,
       6.882292, 6.884375, 6.886458, 6.888542, 6.890625, 6.892708, 6.894792,
       6.896875, 6.898958, 6.901042, 6.903125, 6.905208, 6.907292, 6.909375,
       6.911458, 6.913542, 6.915625, 6.917708, 6.919792, 6.921875, 6.923958,
       6.926042, 6.928125, 6.930208, 6.932292, 6.934375, 6.936458, 6.938542,
       6.940625, 6.942708, 6.944792, 6.946875, 6.948958, 6.951042, 6.953125,
       6.955208, 6.957292, 6.959375, 6.961458, 6.963542, 6.965625, 6.967708,
       6.969792, 6.971875, 6.973958, 6.976042, 6.978125, 6.980208, 6.982292,
       6.984375, 6.986458, 6.988542, 6.990625, 6.992708, 6.994792, 6.996875,
       6.998958, 7.001042, 7.003125, 7.005208, 7.007292, 7.009375, 7.011458,
       7.013542, 7.015625, 7.017708, 7.019792, 7.021875, 7.023958, 7.026042,
       7.028125, 7.030208, 7.032292, 7.034375, 7.036458, 7.038542, 7.040625,
       7.042708, 7.044792, 7.046875, 7.048958, 7.051042, 7.053125, 7.055208,
       7.057292, 7.059375, 7.061458, 7.063542, 7.065625, 7.067708, 7.069792,
       7.071875, 7.073958, 7.076042, 7.078125, 7.080208, 7.082292, 7.084375,
       7.086458, 7.088542, 7.090625, 7.092708, 7.094792, 7.096875, 7.098958,
       7.101042, 7.103125, 7.105208, 7.107292, 7.109375, 7.111458, 7.113542,
       7.115625, 7.117708, 7.119792, 7.121875, 7.123958, 7.126042, 7.128125,
       7.130208, 7.132292, 7.134375, 7.136458, 7.138542, 7.140625, 7.142708,
       7.144792, 7.146875, 7.148958, 7.151042, 7.153125, 7.155208, 7.157292,
       7.159375, 7.161458, 7.163542, 7.165625])</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>y</span></div><div class='xr-var-dims'>(y)</div><div class='xr-var-dtype'>float64</div><div class='xr-var-preview xr-preview'>9.236 9.234 9.232 ... 8.893 8.891</div><input id='attrs-1b66630e-1ac3-4fed-8cc3-94d02352ed48' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-1b66630e-1ac3-4fed-8cc3-94d02352ed48' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-0b63a6d2-24c6-4153-90af-b7a8cbbb8859' class='xr-var-data-in' type='checkbox'><label for='data-0b63a6d2-24c6-4153-90af-b7a8cbbb8859' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([9.236458, 9.234375, 9.232292, 9.230208, 9.228125, 9.226042, 9.223958,
       9.221875, 9.219792, 9.217708, 9.215625, 9.213542, 9.211458, 9.209375,
       9.207292, 9.205208, 9.203125, 9.201042, 9.198958, 9.196875, 9.194792,
       9.192708, 9.190625, 9.188542, 9.186458, 9.184375, 9.182292, 9.180208,
       9.178125, 9.176042, 9.173958, 9.171875, 9.169792, 9.167708, 9.165625,
       9.163542, 9.161458, 9.159375, 9.157292, 9.155208, 9.153125, 9.151042,
       9.148958, 9.146875, 9.144792, 9.142708, 9.140625, 9.138542, 9.136458,
       9.134375, 9.132292, 9.130208, 9.128125, 9.126042, 9.123958, 9.121875,
       9.119792, 9.117708, 9.115625, 9.113542, 9.111458, 9.109375, 9.107292,
       9.105208, 9.103125, 9.101042, 9.098958, 9.096875, 9.094792, 9.092708,
       9.090625, 9.088542, 9.086458, 9.084375, 9.082292, 9.080208, 9.078125,
       9.076042, 9.073958, 9.071875, 9.069792, 9.067708, 9.065625, 9.063542,
       9.061458, 9.059375, 9.057292, 9.055208, 9.053125, 9.051042, 9.048958,
       9.046875, 9.044792, 9.042708, 9.040625, 9.038542, 9.036458, 9.034375,
       9.032292, 9.030208, 9.028125, 9.026042, 9.023958, 9.021875, 9.019792,
       9.017708, 9.015625, 9.013542, 9.011458, 9.009375, 9.007292, 9.005208,
       9.003125, 9.001042, 8.998958, 8.996875, 8.994792, 8.992708, 8.990625,
       8.988542, 8.986458, 8.984375, 8.982292, 8.980208, 8.978125, 8.976042,
       8.973958, 8.971875, 8.969792, 8.967708, 8.965625, 8.963542, 8.961458,
       8.959375, 8.957292, 8.955208, 8.953125, 8.951042, 8.948958, 8.946875,
       8.944792, 8.942708, 8.940625, 8.938542, 8.936458, 8.934375, 8.932292,
       8.930208, 8.928125, 8.926042, 8.923958, 8.921875, 8.919792, 8.917708,
       8.915625, 8.913542, 8.911458, 8.909375, 8.907292, 8.905208, 8.903125,
       8.901042, 8.898958, 8.896875, 8.894792, 8.892708, 8.890625])</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span>spatial_ref</span></div><div class='xr-var-dims'>()</div><div class='xr-var-dtype'>int64</div><div class='xr-var-preview xr-preview'>0</div><input id='attrs-266f2383-a1ad-4e8f-a65a-87db2e08b28e' class='xr-var-attrs-in' type='checkbox' ><label for='attrs-266f2383-a1ad-4e8f-a65a-87db2e08b28e' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-d58fc55c-56eb-481a-a0b6-5aedc33e96ce' class='xr-var-data-in' type='checkbox'><label for='data-d58fc55c-56eb-481a-a0b6-5aedc33e96ce' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'><dt><span>crs_wkt :</span></dt><dd>GEOGCS[&quot;WGS 84&quot;,DATUM[&quot;WGS_1984&quot;,SPHEROID[&quot;WGS 84&quot;,6378137,298.257223563,AUTHORITY[&quot;EPSG&quot;,&quot;7030&quot;]],AUTHORITY[&quot;EPSG&quot;,&quot;6326&quot;]],PRIMEM[&quot;Greenwich&quot;,0,AUTHORITY[&quot;EPSG&quot;,&quot;8901&quot;]],UNIT[&quot;degree&quot;,0.0174532925199433,AUTHORITY[&quot;EPSG&quot;,&quot;9122&quot;]],AXIS[&quot;Latitude&quot;,NORTH],AXIS[&quot;Longitude&quot;,EAST],AUTHORITY[&quot;EPSG&quot;,&quot;4326&quot;]]</dd><dt><span>semi_major_axis :</span></dt><dd>6378137.0</dd><dt><span>semi_minor_axis :</span></dt><dd>6356752.314245179</dd><dt><span>inverse_flattening :</span></dt><dd>298.257223563</dd><dt><span>reference_ellipsoid_name :</span></dt><dd>WGS 84</dd><dt><span>longitude_of_prime_meridian :</span></dt><dd>0.0</dd><dt><span>prime_meridian_name :</span></dt><dd>Greenwich</dd><dt><span>geographic_crs_name :</span></dt><dd>WGS 84</dd><dt><span>horizontal_datum_name :</span></dt><dd>World Geodetic System 1984</dd><dt><span>grid_mapping_name :</span></dt><dd>latitude_longitude</dd><dt><span>spatial_ref :</span></dt><dd>GEOGCS[&quot;WGS 84&quot;,DATUM[&quot;WGS_1984&quot;,SPHEROID[&quot;WGS 84&quot;,6378137,298.257223563,AUTHORITY[&quot;EPSG&quot;,&quot;7030&quot;]],AUTHORITY[&quot;EPSG&quot;,&quot;6326&quot;]],PRIMEM[&quot;Greenwich&quot;,0,AUTHORITY[&quot;EPSG&quot;,&quot;8901&quot;]],UNIT[&quot;degree&quot;,0.0174532925199433,AUTHORITY[&quot;EPSG&quot;,&quot;9122&quot;]],AXIS[&quot;Latitude&quot;,NORTH],AXIS[&quot;Longitude&quot;,EAST],AUTHORITY[&quot;EPSG&quot;,&quot;4326&quot;]]</dd><dt><span>GeoTransform :</span></dt><dd>6.822916666055434 0.0020833333331466974 0.0 9.237499999172456 0.0 -0.0020833333331466974</dd></dl></div><div class='xr-var-data'><pre>array(0)</pre></div></li><li class='xr-var-item'><div class='xr-var-name'><span class='xr-has-index'>date</span></div><div class='xr-var-dims'>(date)</div><div class='xr-var-dtype'>datetime64[ns]</div><div class='xr-var-preview xr-preview'>2015-06-26 ... 2020-07-27</div><input id='attrs-86009135-b142-45c9-bb2b-017eb71a33e3' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-86009135-b142-45c9-bb2b-017eb71a33e3' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-55a75091-629a-4dbc-8912-1d15b3a13e0a' class='xr-var-data-in' type='checkbox'><label for='data-55a75091-629a-4dbc-8912-1d15b3a13e0a' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([&#x27;2015-06-26T00:00:00.000000000&#x27;, &#x27;2015-07-12T00:00:00.000000000&#x27;,
       &#x27;2015-07-28T00:00:00.000000000&#x27;, &#x27;2016-06-25T00:00:00.000000000&#x27;,
       &#x27;2016-07-11T00:00:00.000000000&#x27;, &#x27;2016-07-27T00:00:00.000000000&#x27;,
       &#x27;2017-06-26T00:00:00.000000000&#x27;, &#x27;2017-07-12T00:00:00.000000000&#x27;,
       &#x27;2017-07-28T00:00:00.000000000&#x27;, &#x27;2018-06-26T00:00:00.000000000&#x27;,
       &#x27;2018-07-12T00:00:00.000000000&#x27;, &#x27;2018-07-28T00:00:00.000000000&#x27;,
       &#x27;2019-06-26T00:00:00.000000000&#x27;, &#x27;2019-07-12T00:00:00.000000000&#x27;,
       &#x27;2019-07-28T00:00:00.000000000&#x27;, &#x27;2020-06-25T00:00:00.000000000&#x27;,
       &#x27;2020-07-11T00:00:00.000000000&#x27;, &#x27;2020-07-27T00:00:00.000000000&#x27;],
      dtype=&#x27;datetime64[ns]&#x27;)</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-fdae93eb-502b-4a59-89b0-d59714b96cab' class='xr-section-summary-in' type='checkbox'  checked><label for='section-fdae93eb-502b-4a59-89b0-d59714b96cab' class='xr-section-summary' >Data variables: <span>(1)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-var-name'><span>NDVI</span></div><div class='xr-var-dims'>(date, y, x)</div><div class='xr-var-dtype'>float32</div><div class='xr-var-preview xr-preview'>0.5796 0.5933 ... 0.6463 0.6531</div><input id='attrs-434d7bf6-19a4-4b58-8cc5-164f5c2870c5' class='xr-var-attrs-in' type='checkbox' disabled><label for='attrs-434d7bf6-19a4-4b58-8cc5-164f5c2870c5' title='Show/Hide attributes'><svg class='icon xr-icon-file-text2'><use xlink:href='#icon-file-text2'></use></svg></label><input id='data-5dee7079-a2e5-41c4-b4f0-11180581d715' class='xr-var-data-in' type='checkbox'><label for='data-5dee7079-a2e5-41c4-b4f0-11180581d715' title='Show/Hide data repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-var-attrs'><dl class='xr-attrs'></dl></div><div class='xr-var-data'><pre>array([[[0.5796, 0.5933, 0.5727, ..., 0.4792, 0.4792, 0.4863],
        [0.4863, 0.557 , 0.2871, ..., 0.5017, 0.4617, 0.4617],
        [0.5733, 0.31  , 0.2896, ..., 0.3291, 0.3291, 0.4617],
        ...,
        [0.6557, 0.6557, 0.6883, ..., 0.5928, 0.6592, 0.7034],
        [0.6823, 0.7237, 0.7237, ..., 0.6534, 0.6342, 0.6391],
        [0.4703, 0.4703, 0.7237, ..., 0.6839, 0.6342, 0.6391]],

       [[0.3585, 0.4728, 0.4728, ..., 0.6084, 0.5556, 0.5556],
        [0.2919, 0.3978, 0.3872, ..., 0.6084, 0.5354, 0.5354],
        [0.2482, 0.3978, 0.3565, ..., 0.73  , 0.5452, 0.4544],
        ...,
        [0.1006, 0.09  , 0.6895, ..., 0.5015, 0.5246, 0.5098],
        [0.1006, 0.09  , 0.6895, ..., 0.268 , 0.2073, 0.2829],
        [0.8596, 0.8779, 0.8779, ..., 0.2676, 0.2676, 0.2814]],

       [[0.3011, 0.3011, 0.3111, ..., 0.0974, 0.089 , 0.1308],
        [0.2715, 0.2364, 0.2364, ..., 0.0962, 0.0915, 0.1338],
        [0.237 , 0.2209, 0.2209, ..., 0.1277, 0.1382, 0.1338],
        ...,
...
        [0.4607, 0.4607, 0.3711, ..., 0.4332, 0.5082, 0.5295],
        [0.4607, 0.4607, 0.3711, ..., 0.3829, 0.4571, 0.5295],
        [0.2937, 0.2278, 0.4524, ..., 0.4896, 0.4571, 0.3327]],

       [[0.5516, 0.5674, 0.5377, ..., 0.2019, 0.1376, 0.105 ],
        [0.6154, 0.5691, 0.4732, ..., 0.2019, 0.1651, 0.105 ],
        [0.4165, 0.2704, 0.206 , ..., 0.2235, 0.1988, 0.1988],
        ...,
        [0.2695, 0.5224, 0.1691, ..., 0.191 , 0.191 , 0.1599],
        [0.2695, 0.2695, 0.1691, ..., 0.2169, 0.2169, 0.2849],
        [0.4338, 0.5767, 0.5767, ..., 0.3376, 0.3481, 0.2787]],

       [[0.683 , 0.6689, 0.7116, ..., 0.6772, 0.6548, 0.6476],
        [0.683 , 0.6941, 0.6941, ..., 0.7129, 0.6492, 0.6044],
        [0.7102, 0.7048, 0.551 , ..., 0.7125, 0.6724, 0.4667],
        ...,
        [0.5185, 0.5092, 0.507 , ..., 0.6278, 0.5765, 0.6533],
        [0.6613, 0.633 , 0.606 , ..., 0.6022, 0.561 , 0.596 ],
        [0.6625, 0.6428, 0.6564, ..., 0.6259, 0.6463, 0.6531]]],
      dtype=float32)</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-442e7dd9-3cbe-41da-b5ee-d1ddfa3ccf04' class='xr-section-summary-in' type='checkbox'  ><label for='section-442e7dd9-3cbe-41da-b5ee-d1ddfa3ccf04' class='xr-section-summary' >Indexes: <span>(3)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><ul class='xr-var-list'><li class='xr-var-item'><div class='xr-index-name'><div>x</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-3ced0a0a-269e-4284-a966-669212404eee' class='xr-index-data-in' type='checkbox'/><label for='index-3ced0a0a-269e-4284-a966-669212404eee' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(Index([6.8239583327220075,  6.826041666055154,  6.828124999388301,
        6.830208332721448,  6.832291666054594,  6.834374999387741,
        6.836458332720888,  6.838541666054034,  6.840624999387181,
        6.842708332720328,
       ...
        7.146874999359746,  7.148958332692892,  7.151041666026039,
        7.153124999359186,  7.155208332692332,  7.157291666025479,
        7.159374999358626, 7.1614583326917725,  7.163541666024919,
        7.165624999358066],
      dtype=&#x27;float64&#x27;, name=&#x27;x&#x27;, length=165))</pre></div></li><li class='xr-var-item'><div class='xr-index-name'><div>y</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-50d07274-8535-4119-a4eb-c86bfdc208cd' class='xr-index-data-in' type='checkbox'/><label for='index-50d07274-8535-4119-a4eb-c86bfdc208cd' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(Index([9.236458332505883, 9.234374999172736,  9.23229166583959,
       9.230208332506443, 9.228124999173296,  9.22604166584015,
       9.223958332507003, 9.221874999173856,  9.21979166584071,
       9.217708332507563,
       ...
       8.909374999201852, 8.907291665868705, 8.905208332535558,
       8.903124999202412, 8.901041665869265, 8.898958332536118,
       8.896874999202971, 8.894791665869825, 8.892708332536678,
       8.890624999203531],
      dtype=&#x27;float64&#x27;, name=&#x27;y&#x27;, length=167))</pre></div></li><li class='xr-var-item'><div class='xr-index-name'><div>date</div></div><div class='xr-index-preview'>PandasIndex</div><div></div><input id='index-04393a29-6329-42db-bdcb-626f6edb898b' class='xr-index-data-in' type='checkbox'/><label for='index-04393a29-6329-42db-bdcb-626f6edb898b' title='Show/Hide index repr'><svg class='icon xr-icon-database'><use xlink:href='#icon-database'></use></svg></label><div class='xr-index-data'><pre>PandasIndex(DatetimeIndex([&#x27;2015-06-26&#x27;, &#x27;2015-07-12&#x27;, &#x27;2015-07-28&#x27;, &#x27;2016-06-25&#x27;,
               &#x27;2016-07-11&#x27;, &#x27;2016-07-27&#x27;, &#x27;2017-06-26&#x27;, &#x27;2017-07-12&#x27;,
               &#x27;2017-07-28&#x27;, &#x27;2018-06-26&#x27;, &#x27;2018-07-12&#x27;, &#x27;2018-07-28&#x27;,
               &#x27;2019-06-26&#x27;, &#x27;2019-07-12&#x27;, &#x27;2019-07-28&#x27;, &#x27;2020-06-25&#x27;,
               &#x27;2020-07-11&#x27;, &#x27;2020-07-27&#x27;],
              dtype=&#x27;datetime64[ns]&#x27;, name=&#x27;date&#x27;, freq=None))</pre></div></li></ul></div></li><li class='xr-section-item'><input id='section-24076e89-6190-4d41-b49d-d1ab1f1d3a84' class='xr-section-summary-in' type='checkbox' disabled ><label for='section-24076e89-6190-4d41-b49d-d1ab1f1d3a84' class='xr-section-summary'  title='Expand/collapse section'>Attributes: <span>(0)</span></label><div class='xr-section-inline-details'></div><div class='xr-section-details'><dl class='xr-attrs'></dl></div></li></ul></div></div>




```python
# Compute the difference in NDVI before and after the fire
abuja_diff = (
    abuja_da
        .sel(date=slice('2017', '2020'))
        .mean('date')
        .NDVI 
   - abuja_da
        .sel(date=slice('2015', '2017'))
        .mean('date')
        .NDVI
)
```


```python
# Plot the difference
fig, ax = plt.subplots()

abuja_diff.plot(x='x', y='y', cmap='PiYG', robust=True, ax=ax)
Gwagwalada.plot(edgecolor='black', color='none', ax=ax)
plt.show()
```


    
![png](vegetation_files/vegetation_43_0.png)
    



```python
# Save Fig as Image
fig.savefig('gwagwalada.jpg')
```


```python
Gwagwalada_out_gdf = (
    gpd.GeoDataFrame(geometry=Gwagwalada.envelope)
    .overlay(Gwagwalada, how='difference'))
Gwagwalada_out_gdf
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>MULTIPOLYGON (((6.82315 8.88978, 6.82315 9.109...</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Clip data to both inside and outside the boundary
ndvi_abuja_da = abuja_da.rio.clip(Gwagwalada.geometry, from_disk=True)
ndvi_abuja_out_da = abuja_da.rio.clip(Gwagwalada_out_gdf.geometry, from_disk=True)
```


```python
# Compute mean annual July NDVI
jul_ndvi_abuja_df = (
    ndvi_abuja_da
    .groupby(ndvi_abuja_da.date.dt.year)
    .mean(...)
    .NDVI.to_dataframe())
jul_ndvi_abuja_out_df = (
    ndvi_abuja_out_da
    .groupby(ndvi_abuja_out_da.date.dt.year)
    .mean(...)
    .NDVI.to_dataframe())

# Plot inside and outside the reservation
abuja_jul_ndvi_df = (
    jul_ndvi_abuja_df[['NDVI']]
    .join(
        jul_ndvi_abuja_out_df[['NDVI']], 
        lsuffix=' Inside Gwagwalada', rsuffix=' Outside Gwagwalada')
)
```


```python
# Plot difference inside and outside the reservation
abuja_jul_ndvi_df.plot(
    title='NDVI before and after 2017; Gwagwalada, Abuja, Nigeria')
```




    <Axes: title={'center': 'NDVI before and after 2017; Gwagwalada, Abuja, Nigeria'}, xlabel='year'>




    
![png](vegetation_files/vegetation_48_1.png)
    



```python
import nbformat

# Path to the notebook file
notebook_path = "vegetation.ipynb"

# Load the notebook
with open(notebook_path, "r", encoding="utf-8") as f:
    notebook = nbformat.read(f, as_version=4)

# Iterate through each cell in the notebook
for cell in notebook.cells:
    if cell.cell_type == "markdown":
        # Check if the Markdown cell contains a reference to the image
        if "" in cell.source:
            # Remove the reference from the Markdown cell
            cell.source = cell.source.replace("", "")

# Save the modified notebook back to the file
with open(notebook_path, "w", encoding="utf-8") as f:
    nbformat.write(notebook, f)

```


```python
!jupyter nbconvert *.ipynb --to html


```

    [NbConvertApp] Converting notebook vegetation.ipynb to html
    Traceback (most recent call last):
      File "/opt/conda/bin/jupyter-nbconvert", line 10, in <module>
        sys.exit(main())
                 ^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/jupyter_core/application.py", line 280, in launch_instance
        super().launch_instance(argv=argv, **kwargs)
      File "/opt/conda/lib/python3.11/site-packages/traitlets/config/application.py", line 1075, in launch_instance
        app.start()
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/nbconvertapp.py", line 420, in start
        self.convert_notebooks()
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/nbconvertapp.py", line 597, in convert_notebooks
        self.convert_single_notebook(notebook_filename)
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/nbconvertapp.py", line 563, in convert_single_notebook
        output, resources = self.export_single_notebook(
                            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/nbconvertapp.py", line 487, in export_single_notebook
        output, resources = self.exporter.from_filename(
                            ^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/exporters/templateexporter.py", line 386, in from_filename
        return super().from_filename(filename, resources, **kw)  # type:ignore[return-value]
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/exporters/exporter.py", line 201, in from_filename
        return self.from_file(f, resources=resources, **kw)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/exporters/templateexporter.py", line 392, in from_file
        return super().from_file(file_stream, resources, **kw)  # type:ignore[return-value]
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/exporters/exporter.py", line 220, in from_file
        return self.from_notebook_node(
               ^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/exporters/html.py", line 268, in from_notebook_node
        html, resources = super().from_notebook_node(nb, resources, **kw)
                          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/exporters/templateexporter.py", line 424, in from_notebook_node
        output = self.template.render(nb=nb_copy, resources=resources)
                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/jinja2/environment.py", line 1301, in render
        self.environment.handle_exception()
      File "/opt/conda/lib/python3.11/site-packages/jinja2/environment.py", line 936, in handle_exception
        raise rewrite_traceback_stack(source=source)
      File "/opt/conda/share/jupyter/nbconvert/templates/lab/index.html.j2", line 4, in top-level template code
        {% include 'jupyter_widgets.html.j2' %}

        ^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/share/jupyter/nbconvert/templates/lab/base.html.j2", line 3, in top-level template code
        {% from 'cell_id_anchor.j2' import cell_id_anchor %}
        ^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/share/jupyter/nbconvert/templates/base/display_priority.j2", line 1, in top-level template code
        {%- extends 'base/null.j2' -%}
        ^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/share/jupyter/nbconvert/templates/base/null.j2", line 26, in top-level template code
        {%- block body -%}
        ^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/share/jupyter/nbconvert/templates/base/null.j2", line 29, in block 'body'
        {%- block body_loop -%}
    ^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/share/jupyter/nbconvert/templates/base/null.j2", line 31, in block 'body_loop'
        {%- block any_cell scoped -%}
    ^^^^^^^^^^^^^^^^^
      File "/opt/conda/share/jupyter/nbconvert/templates/base/null.j2", line 87, in block 'any_cell'
        {%- block markdowncell scoped-%} {%- endblock markdowncell -%}
    ^^^^^
      File "/opt/conda/share/jupyter/nbconvert/templates/lab/base.html.j2", line 109, in block 'markdowncell'
        {%- set html_value=cell.source  | markdown2html | strip_files_prefix -%}
        ^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/exporters/html.py", line 243, in markdown2html
        return MarkdownWithMath(renderer=renderer).render(source)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/filters/markdown_mistune.py", line 485, in render
        return str(super().__call__(source))
                   ^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/mistune/markdown.py", line 110, in __call__
        return self.parse(s)[0]
               ^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/mistune/markdown.py", line 90, in parse
        result = self.render_state(state)
                 ^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/mistune/markdown.py", line 48, in render_state
        return self.renderer(data, state)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/mistune/core.py", line 209, in __call__
        return self.render_tokens(tokens, state)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/mistune/core.py", line 206, in render_tokens
        return ''.join(self.iter_tokens(tokens, state))
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/mistune/core.py", line 203, in iter_tokens
        yield self.render_token(tok, state)
              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/mistune/renderers/html.py", line 34, in render_token
        text = self.render_tokens(token['children'], state)
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/mistune/core.py", line 206, in render_tokens
        return ''.join(self.iter_tokens(tokens, state))
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/mistune/core.py", line 203, in iter_tokens
        yield self.render_token(tok, state)
              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/mistune/renderers/html.py", line 41, in render_token
        return func(text, **attrs)
               ^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/filters/markdown_mistune.py", line 374, in image
        url = self._embed_image_or_attachment(url)
              ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
      File "/opt/conda/lib/python3.11/site-packages/nbconvert/filters/markdown_mistune.py", line 391, in _embed_image_or_attachment
        raise InvalidNotebook(msg)
    nbconvert.filters.markdown_mistune.InvalidNotebook: missing attachment: ../../img/earth-analytics/remote-sensing/spectral_vegetation_stress.png
