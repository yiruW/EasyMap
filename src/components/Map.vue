<template>
    <SearchBar @query="searchLocation"></SearchBar>
    <div class="map" id="map"></div>
    <GetLocationButton @displayMyLocation="displayMyLocation"></GetLocationButton>
    <RecordTable :records=this.records.slice().reverse() @deleteRecord="deleteRecord"></RecordTable>
</template>

<script>
import SearchBar from "./SearchBar.vue"
import GetLocationButton from "./GetLocationButton.vue"
import RecordTable from "./RecordTable.vue"

export default {
    name: 'Map',
    components: {
        SearchBar,
        GetLocationButton,
        RecordTable
    },
    data:() => ({
        map: null,
        infowindow: null,
        records: [],
        id: 0,
        markers: [],
    }),
    methods: {
        initMap(){
            this.map = new google.maps.Map(document.getElementById("map"), {
                center: this.records.length ? this.records[this.records.length - 1].location : new google.maps.LatLng(43.4722854, -80.5448576),
                zoom: 15,
            })
            this.infowindow = new google.maps.InfoWindow()
            this.id = this.records.length ? this.records[this.records.length-1].id + 1 : 0
            for (const record of this.records) this.createMarker(record.id, record.name, record.location)
            
        },
        createMarker(id, name, location) {
            const marker = new google.maps.Marker({
                map: this.map,
                position: location,
            });
            google.maps.event.addListener(marker, "click", () => {
                this.infowindow.setContent(name)
                this.infowindow.open(this.map, marker)
            });
            this.markers[id] = marker
        },
        searchLocation(query) {
            const request = {
                query: query,
                fields: ["name", "place_id"],
            };
            
            try{
                const service = new google.maps.places.PlacesService(this.map);
                service.findPlaceFromQuery(request, (results, status) => {
                    if (status === google.maps.places.PlacesServiceStatus.OK && results) {
                        if (!results[0].place_id) return;
                        this.getDetailInfo(service, results[0].place_id)
                    }
                });
            } catch(error) {
                console.log(error)
            }
        },
        displayMyLocation(location) {
            this.map.setCenter(location)
            this.addRecordToTable('My Location', location, -new Date().getTimezoneOffset())
        },
        getDetailInfo(service, placeId) {
            const request = {
                placeId: placeId
            }
            service.getDetails(request, (result, status) => {
                if (status === google.maps.places.PlacesServiceStatus.OK && result){
                    const resultLocation = result.geometry.location
                    this.map.setCenter(resultLocation)
                    this.addRecordToTable(result.name, resultLocation, result.utc_offset_minutes)
                }
            });
        },
        addRecordToTable(name, location, utcOffset){
            const time = new Date(Date.now() + 60000 * (utcOffset + new Date().getTimezoneOffset())).toString()
            const timeZone = 'GMT' + utcOffset/60
            const localTime = time.split(' GMT')[0]
            const id = this.id ++
            this.createMarker(id, name, location)
            const newRecord = {
                id: id,
                name: name,
                location: location,
                timeZone: timeZone,
                localTime: localTime,
            }
            this.records.push(newRecord)
            this.saveRecords()
        },
        saveRecords() {
            const parsed = JSON.stringify(this.records);
            localStorage.setItem('records', parsed);
        },
        deleteRecord(items) {
            for (let item of items){
                this.records.splice(this.records.findIndex(r => r.id === item.id), 1);
                this.removeMarker(item.id)
            }
            this.saveRecords()
            this.map.setCenter(this.records.length ? this.records[this.records.length - 1].location : new google.maps.LatLng(43.4722854, -80.5448576),)
        },
        removeMarker(id) {
            if (this.markers[id]) {
                google.maps.event.addListener(this.markers[id],"click",function(){});
                this.markers[id].setVisible(false);
                this.markers[id].setMap(null);
                this.markers[id] = null;
            };
        }
    },
    mounted() {
        if(localStorage.getItem('records')) {
            try{
                this.records = JSON.parse(localStorage.getItem('records'));
            } catch(e) {
                localStorage.removeItem('records');
            }
        }
        this.initMap()
    }
}
</script>

<style scoped>
.map {
  width: 100%;
  min-height: 400px;
}
</style>
