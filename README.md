# Glance-Plugin

This directory collects a series of custom plugins, designed to extend the functionality of Glance.

## 📂 JellyStats

A small widget that gives you some data about your Jellyfin library.

<details>
  <summary><strong> YAML Configuration for Glance</strong></summary>
  
  ```yaml
      - type: custom-api
        title: 📊 Statistiche Jellyfin
        cache: 30m
        url: "${JELLYFIN_URL}/Users/${JELLYFIN_USER_ID}/Items"
        parameters:
          IncludeItemTypes: Movie,Series,Episode
          Recursive: true
          Fields: RunTimeTicks
          PageSize: 10000
        headers:
          Accept: application/json
          X-Emby-Token: ${JELLYFIN_API_KEY}
        template: |
          {{- $items        := .JSON.Array "Items" }}
          {{- $movieCount   := 0 }}
          {{- $seriesCount  := 0 }}
          {{- $episodeCount := 0 }}
          {{- $ticksMovies  := 0 }}
          {{- $ticksEps     := 0 }}
          {{- range $items }}
            {{- $t := .String "Type" }}
            {{- if eq $t "Movie" }}
              {{- $movieCount  = add $movieCount 1 }}
              {{- $ticksMovies = add $ticksMovies (.Int "RunTimeTicks") }}
            {{- else if eq $t "Series" }}
              {{- $seriesCount = add $seriesCount 1 }}
            {{- else if eq $t "Episode" }}
              {{- $episodeCount = add $episodeCount 1 }}
              {{- $ticksEps     = add $ticksEps (.Int "RunTimeTicks") }}
            {{- end }}
          {{- end }}

          {{- $hoursMovies := div (div $ticksMovies 10000000) 3600 }}
          {{- $hoursSeries := div (div $ticksEps     10000000) 3600 }}

          <div style="display: grid; grid-template-columns: repeat(2,1fr); gap: 0.5rem;">
            <!-- RIGA 1 -->
            <div style="padding:1rem; border-radius:var(--border-radius); text-align:center;">
              <div style="font-size:1.5rem; font-weight:bold;">{{ $movieCount }}</div>
              <div>Totale Film</div>
            </div>
            <div style="padding:1rem; border-radius:var(--border-radius); text-align:center;">
              <div style="font-size:1.5rem; font-weight:bold;">{{ $seriesCount }} ({{ $episodeCount }} episodi)</div>
              <div>Totale Serie</div>
            </div>

            <!-- SEPARATORE -->
            <div style="grid-column: span 2; height:1px; background:rgba(255,255,255,0.05); margin:0.25rem 0;"></div>

            <!-- RIGA 2 -->
            <div style="padding:1rem; border-radius:var(--border-radius); text-align:center;">
              <div style="font-size:1.5rem; font-weight:bold;">{{ $hoursMovies }}</div>
              <div>Ore di Film</div>
            </div>
            <div style="padding:1rem; border-radius:var(--border-radius); text-align:center;">
              <div style="font-size:1.5rem; font-weight:bold;">{{ $hoursSeries }}</div>
              <div>Ore di Serie</div>
            </div>
          </div>


  ```
</details>

Remember to put in your .env 

JELLYFIN_URL= YOUR IP 

JELLYFIN_API_KEY= API KEY

JELLYFIN_USER_ID= JELLYFIN USER ID  


