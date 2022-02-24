<template>
    <div
        id="mainapp"
        :class="
            typeof weather.main !== 'undefined' && weather.main.temp > 16
                ? 'sunny'
                : ''
        "
    >
        <main>
            <div class="form-group">
                <div>
                    <input
                        placeholder="Country/City"
                        type="text"
                        class="w-50 offset-3 form-control"
                        v-model="query"
                        @keypress="fetchWeather"
                    />
                    <!-- {{ query }} -->

                    <div class="error-wrap" v-if="error !== ''">
                        <h2 class="danger">{{ error }}</h2>
                    </div>
                </div>
            </div>

            <div class="weather-wrap" v-if="typeof weather.main !== 'undefined'">
                <div class="location-box">
                    <div class="location">
                        {{ weather.name }}, {{ weather.sys.country }}
                    </div>

                    <div class="date">{{ dateBuilder() }}</div>
                </div>
                <div class="weather-box">
                    <div class="temp">
                        {{ Math.round(weather.main.temp) }}°C
                    </div>
                    <div class="weather"> {{ weather.weather[0].main }} </div>
                </div>
            </div>
        </main>
    </div>
</template>

<script>
export default {
    name: "WeatherAppComponent.vue",
    data() {
        return {
            query: "",
            app_key: "7862562e8c4afdf7accd95ffeda4515d",
            base_url: "https://api.openweathermap.org/data/2.5/",
            weather: {},
            error: "",
        };
    },
    methods: {
        fetchWeather: function (e) {
            if (e.key == "Enter") {
                this.setError("");

                const url = `${this.base_url}weather?q=${this.query}&units=metric&APPID=${this.app_key}`;

                fetch(url)
                    .then(async (res) => {
                        const data = await res.json();

                        if (!res.ok) {
                            if (
                                data.message !== true &&
                                data.message === "city not found"
                            ) {
                                throw new Error("Country/City not found.");
                            }

                            throw new Error("Something went wrong.");
                        }
                        this.setResults(data);
                    })
                    .catch((error) => {
                        this.setResults({});
                        this.setError(error.message);
                    });
            }
        },

        setResults: function (results) {
            this.weather = results;
        },

        setError: function (message) {
            this.error = message;
        },

        dateBuilder: function () {
            const d = new Date();

            const days = [
                "Sunday",
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday",
                "Saturday",
            ];

            const months = [
                "January",
                "February",
                "March",
                "April",
                "May",
                "June",
                "July",
                "August",
                "September",
                "October",
                "November",
                "December",
            ];

            const day = days[d.getDay()];
            const month = months[d.getMonth()].substring(0, 3);
            const date = d.getDate();
            const year = d.getFullYear();

            return `${day} ${month} ${date}, ${year}`;
        },
    },
};
</script>

<style scoped>
#mainapp {
    background-image: url("/assets/coldgif.gif");
    background-size: cover;
    background-position: center;
    transition: 0.4s;
}
#mainapp.sunny {
    background-image: url("/assets/sunny.gif");
    /* background-size: cover;
    background-position: bottom;
    transition: 0.4s; */
}
main {
    min-height: 100vh;
    padding: 25px;
    background-image: linear-gradient(
        to bottom,
        rgba(0, 0, 0, 0.25),
        rgba(0, 0, 0, 0.75)
    );
}
.location-box .location {
    color: #fff;
    font-size: 32px;
    font-weight: 500;
    text-align: center;
    text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.location-box .date {
    color: #fff;
    font-size: 20px;
    font-weight: 300;
    text-align: center;
    font-style: italic;
}
.weather-box {
    text-align: center;
}
.weather-box .temp {
    display: inline-block;
    padding: 10px 25px;
    color: #fff;
    font-size: 102px;
    font-weight: 900;
    text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
    background-color: rgba(255, 255, 255, 0.25);
    border-radius: 16px;
    margin: 30px 0px;
    box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather {
    color: #fff;
    font-size: 48px;
    font-weight: 700;
    font-style: italic;
    text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.error-wrap {
    width: 100%;
    margin: 20px 0;
    text-align: center;
    color: red;
}
</style>