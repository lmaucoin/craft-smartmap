# Smart Maps

Smart Map is a plugin which allows you to easily manage geographic points. With Smart Map, you can:

- Display multiple locations on a dynamic map
- Search entries to find the closest location

Once you have installed the Smart Map plugin, simply create a new field using the "Smart Map Address" field type, and add it to a section. You'll see the magic once you start editing entries in that section!

## Using a "Smart Map Address" field

As you begin entering the address, a list of possible matches will appear. You can simply click on the address which matches the one you are entering, and the remaining fields will be filled in automatically.

You may see an address option which is very close to correct, but perhaps some of the information is a little off (perhaps it's showing the correct zip code, but a neighboring city name). You can easily correct any of the address information after picking out the closest match. This allows the latitude and longitude to be calculated automatically.

It's the latitude and longitude that are so important to Smart Map... This information is used to plot each address on a map. It is also used when searching through existing addresses, to determine which of them is closest to your target.

## Requirements
 - Craft (obviously)
 - Google Maps API key - <a href="https://developers.google.com/maps/documentation/javascript/tutorial#api_key" target="_blank">Get yours here...</a>

---------------------------------------

## Using your Smart Map field values in a template

_* From this example, replace "myAddressField" with the name of your Smart Map Address field_

How to display your Smart Map Address values:

    {% for entry in craft.entries.section('myLocations').find() %}
        <h1>{{ entry.title }}</h1>
        <div>
            {{ entry.myAddressField.street1 }}<br />
            {{ entry.myAddressField.street2 }}<br />
            {{ entry.myAddressField.city }}, {{ entry.myAddressField.state }} {{ entry.myAddressField.zip }}<br>
        </div>
        <div>
            Latitude: {{ entry.myAddressField.lat }}<br />
            Longitude: {{ entry.myAddressField.lng }}
        </div>
    {% endfor %}

---------------------------------------

## How to render a Google Map of your locations

    {{ craft.smartMap.map(locations, options) }}

This will create a &lt;div&gt; tag which will automatically be used as a container for your Google Map. By default, the tag will have a "smartmap-mapcanvas" class

    {% set locations = craft.entries.section('myLocations').find() %}
    {% set options = {
        'zoom': 5,
        'height': 450
    } %}
    
    {{ craft.smartMap.map(locations, options) }}

<table>
    <tr>
        <th>Options</th>
        <th>Type</th>
        <th>Default</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>id</td>
        <td>string</td>
        <td>"smartmap-mapcanvas"</td>
        <td>Set id attribute of container.</td>
    </tr>
    <tr>
        <td>width</td>
        <td>int</td>
        <td><em>null</em></td>
        <td>Width of map (in px)</td>
    </tr>
    <tr>
        <td>height</td>
        <td>int</td>
        <td><em>null</em></td>
        <td>Height of map (in px)</td>
    </tr>
    <tr>
        <td>zoom</td>
        <td>int</td>
        <td>8</td>
        <td>Zoom level <em>(higher number = greater detail)</em></td>
    </tr>
    <tr>
        <td>center</td>
        <td>array</td>
        <td>(avg. of all coords)</td>
        <td>Map center, ie: {'lat':38.897837, 'lng':-77.036512}</td>
    </tr>
</table>

---------------------------------------

## How to sort entries by closest locations

Include this in your "craft.entries" call:

    .myAddressField(params).order('distance')

This will tell Craft to:
 - Filter your Smart Map field with the parameter you specify.
 - Order the results by closest distance from your specified target.

_* From this example, replace "myAddressField" with the name of your Smart Map Address field_


    {% set target = '90210' %}
    {% set params = {
        target: target,
        range: 100
    } %}

    {% set entries = craft.entries.myAddressField(params).order('distance').find() %}

    <h1>Showing results for "{{ target }}"...</h1>
    {% for entry in entries %}
        <h2>{{ entry.title }}</h2>
        <div>
            {{ entry.myAddressField.street1 }}<br />
            {{ entry.myAddressField.street2 }}<br />
            {{ entry.myAddressField.city }}, {{ entry.myAddressField.state }} {{ entry.myAddressField.zip }}<br>
            <strong>{{ entry.myAddressField.distance | number_format(1) }} miles away</strong>
        </div>
    {% else %}
        <h2>No results found</h2>
    {% endfor %}

The "target" parameter is required. All others are optional.

Your target can be anything that translates into a full or partial address...
 - 90210
 - Aurora, IL
 - 742 Evergreen Terrace

<table>
    <tr>
        <th>Parameter</th>
        <th>Default</th>
        <th>Description</th>
    </tr>
    <tr>
        <td>target</td>
        <td>&nbsp;</td>
        <td>[REQUIRED] Starting point for proximity search</td>
    </tr>
    <tr>
        <td>range</td>
        <td>25</td>
        <td>Search radius, measured in "units"</td>
    </tr>
    <tr>
        <td>units</td>
        <td>"miles"</td>
        <td>Units of measurement ("miles" or "kilometers")</td>
    </tr>
</table>