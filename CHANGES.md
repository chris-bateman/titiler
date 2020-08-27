# Release Notes

## 0.0.1 (2020-08-27)

**First release on pypi**

### Tiler Factory

For this release we created new Tiler Factories class which handle creation of FastAPI routers for a given rio_tiler **Readers**.

```python
from titiler.endpoints.factory import TilerFactory
from rio_tiler_crs import COGReader, STACReader

from fastapi import FastAPI

app = FastAPI()

cog = TilerFactory()
app.include_router(cog.router, tags=["Cloud Optimized GeoTIFF"])

stac = TilerFactory(reader=STACReader, add_asset_deps=True, router_prefix="stac")
app.include_router(cog.router, prefix="/stac", tags=["Cloud Optimized GeoTIFF"])
```

### Readers / TileMatrixSets

The tiler factory will automatically fallback to WebMercator TMS if the `Reader` doesn't support TMS.

```python
from rio_tiler_crs import COGReader as COGReaderWithTMS
from rio_tiler.io import COGReader as COGReaderNoTMS

from titiler.endpoints import factory
from titiler.dependencies import WebMercatorTMSParams, TMSParams

app = factory.TilerFactory(reader=COGReaderWithTMS)
assert app.reader_supports_tms
assert app.tms_dependency == TMSParams

app = factory.TilerFactory(reader=COGReaderNoTMS)
assert not app.reader_supports_tms
assert app.tms_dependency == WebMercatorTMSParams
```

### Other changes

* add mosaic support  (#17 author @geospatial-jeff)
* update to rio-tiler-crs>=3.0b* and rio-tiler>=2.0b*
* Pin fastapi version to 0.60.1
* Remove titiler.core in favor of starlette settings (#55, author @geospatial-jeff)
* Add fastapi exception handlers (#56, author @geospatial-jeff)
* Remove intermediary routers (#57, author @geospatial-jeff)
* Remove /titiler/api submodule (e.g titiler.api.utils -> titiler.utils)
* Add Cache-Control middleware. Endpoints do not define any cache-control headers. (part of #43, co-author with @geospatial-jeff)
* Add 'X-Assets' in response headers for mosaic tiles (#51)
* add cog validation via rio-cogeo (co-author with @geospatial-jeff, #37)

### Breaking changes 
* removed cache layer for tiles
* updated html templates

```python
template_dir = pkg_resources.resource_filename("titiler", "templates")
templates = Jinja2Templates(directory=template_dir)

cog_template = templates.TemplateResponse(
    name="cog_index.html",
    context={
        "request": request,
        "tilejson": request.url_for("cog_tilejson"),
        "metadata": request.url_for("cog_metadata"),
    },
    media_type="text/html",
)

stac_template = templates.TemplateResponse(
    name="stac_index.html",
    context={
        "request": request,
        "tilejson": request.url_for("stac_tilejson"),
        "metadata": request.url_for("stac_info"),
    },
    media_type="text/html",
)
```

## Pre Pypi releases

## 2.1.2 (2020-06-24)

* add `width` & `height` parameters in API docs to force output size for part/preview endpoints.
* add `resampling_method` in API docs.

link: https://github.com/developmentseed/titiler/commit/725da5fa1bc56d8e192ae8ff0ad107493ca93378

## 2.1.1 (2020-06-22)

* add minimum fastapi version (0.54.0) and update docker config

link: https://github.com/developmentseed/titiler/commit/95b98a32ffb3274d546dd52f99a3920091029b4c

## 2.1.0 (2020-06-11)

* add `/preview`, `/crop`, `/point` endpoints

link: https://github.com/developmentseed/titiler/commit/8b63fc6b6141b9c9361c95d80897d77b5e2d47c3

## 2.0.0 (2020-06-09)

* support STAC items (#16)
* better API documentation via response models
* update UI (`/stac/viewer`, `/cog/viewer`)
* re-order OpenAPI route tags
* update documentation

link: https://github.com/developmentseed/titiler/commit/fa2cb78906b0fd88506b89bace8174969be8cd4f

## 1.0.0 (2020-06-04)

Initial release

link: https://github.com/developmentseed/titiler/commit/f4fdc02ea0235470589eeb34a4da8e5aae74e696