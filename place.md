# Search an ID to get details on a place

When you know an identification number and the source it came from, you can use Mapzen Search to get details on the location.

The `/place` endpoint accepts Mapzen Search `gid` strings that get returned for every exactly matched record in query responses. These `gid` strings should not be built manually, but rather used directly as-is to lookup additional details on the location that `gid` refers to.

For example, this `/place` query looks up the Eiffel Tower in OpenStreetMap (OSM):

> [/v1/place?api_key=your-mapzen-api-key&__ids=openstreetmap:venue:way:5013364__](https://mapzen.com/search/explorer/?query=place&ids=openstreetmap:venue:way:5013364)

Note that you need an actual `gid` value to make a `/place` search. For example, if you search for an address and the result is [interpolated](addresses.md#address-interpolation), then there is no discrete `gid` to use for a `/place` search because interpolated results may be from multiple data sources.

## Search for multiple places in a query

To search for more than one `/place` in a request, join multiple values together and separate them with a comma. For example, this `/place` query looks up the Eiffel Tower in OpenStreetMap and the borough of Manhattan in Who's on First:

> [/v1/place?api_key=your-mapzen-api-key&__ids=openstreetmap:venue:way:5013364,whosonfirst:borough:421205771__](https://mapzen.com/search/explorer/?query=place&ids=openstreetmap:venue:way:5013364,whosonfirst:borough:421205771)

The results are returned in the order requested.

If you enter a valid `gid` that cannot be found or has "expired" due to a newer build, you may get empty results. The request will NOT return an error.

If the structure of your `gid` is invalid, an error will be returned as part of the GeoJSON structure.

Keep in mind that if you enter a `gid` that cannot be found in a list of multiple IDs, then the `features` array in the response contains a different number of elements than the number of requests. For example, your request may have three IDs requested but only two results returned. The reason for this is that the `features` section of the response is GeoJSON-compliant, and JSON does not allow a way to convey an exception condition (not even an empty JSON element, `{}`). For this reason, if your application is dependent upon the results mapping directly to the individual input requests in order, then you'll have to do your own bookkeeping to handle exception conditions.

:warning: It is important to not use any `gid` values to attempt `/place` queries after longer than an hour. These IDs are not intended to be stable across builds, as datasets are used that do not have consistent IDs.
