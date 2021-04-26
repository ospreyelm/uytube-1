<!DOCTYPE html>
<meta charset="UTF-8">
<head>

  <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto+Condensed:400,400i,700,700i" />
  <style>
    body, .font_a, button, input, input  { font-family: "Roboto Condensed"; }
    table, table button { font-size: 0.9em; }
    .row { display: flex; }
    .column { /*flex: 50%;*/ margin-left: 25px; }
  </style>

</head>
<html>
<body>
<div class="row">
  <div class="column">

    <div>
      <button style="margin-top:0.5em;margin-bottom:0.5em;font-size:1em" onclick="player.playVideo();player.seekTo(0, true);playSVG(0)">
        <!-- To do: skip long lead-ins -->
        PLAY VIDEO AND SVG ANIMATION
      </button>
    </div>

    <!-- Co-ordinate SVG -->
    <script>
      function playSVG(cue=null) {
        if (cue == null) {
          cue = Math.round(fs * player.getCurrentTime()) / fs;
        }
        let svg = document.getElementById('form_clock');
        svg.setCurrentTime(cue);
        if ( ![0,2].includes(player.getPlayerState()) ) {
          svg.unpauseAnimations();
        }
      }
      function pauseSVG() {
        let svg = document.getElementById('form_clock');
        svg.pauseAnimations();
      }
      function renderSVG(newData) {
        let svg = document.getElementById('form_clock');
        svg.setCurrentTime(0);
        replaceData(newData);
        drawForm();
        cue = Math.round(fs * player.getCurrentTime()) / fs;
        svg.setCurrentTime(cue);
      }
    </script>

    <!-- SVG -->
    <svg id="form_clock" xmlns="http://www.w3.org/2000/svg"
    xmlns:xlink="http://www.w3.org/1999/xlink"
    width="304px" height="680px" viewBox="0 0 304 680" 
    onload="drawForm();pauseSVG()">
    <!-- width="304px" height="540px" viewBox="0 0 304 540"  -->

      <rect height="100%" width="100%" fill="#FAFAFA"></rect>

      <!-- Based on SVG animated clock with Javascript at https://www.bogotobogo.com/SVG/svg_animation_with_source.php -->
      <!-- This is a modified version from Charles McCathieNevile's code
        1. Javascript added to set the time as current
        2. Removed animation code
        3. Other minor changes -->

      <g transform="translate(135,135)" id="svg_template">

        <!-- preplay -->
        <g transform="rotate(270)" id="preplaywrapper" opacity="0">
          <line stroke-width="2" y1="-15" y2="40" stroke-linecap="round" stroke="black" opacity=".9"/>
            <animateTransform id="preplay" attributeName="transform" type="rotate"
              repeatCount="1" fill="freeze" dur="1h" by="-180" begin="0s"/>
          <circle r="4" fill="black" opacity=".9"/>
          <!-- following dur value is hardcoded!! -->
          <animate id="showpreplay" attributeName="opacity" to="1"
            dur=".1s" fill="freeze" begin="1h"/>
          <animate id="hidepreplay" attributeName="opacity" to="0"
            dur=".4s" fill="freeze" begin="1h"/>
        </g>

        <!-- countdown -->
        <g transform="rotate(90)" id="readywrapper" opacity="0">
          <line stroke-width="2" y1="-15" y2="40" stroke-linecap="round" stroke="black" opacity=".9"/>
            <animateTransform id="ready" attributeName="transform" type="rotate"
              repeatCount="1" fill="freeze" dur="1h" by="180" begin="1h"/>
          <circle r="4" fill="black" opacity=".9"/>
          <!-- following dur value is hardcoded!! -->
          <animate id="showready" attributeName="opacity" to="1"
            dur="1s" fill="freeze" begin="1h"/>
          <animate id="hideready" attributeName="opacity" to="0"
            dur=".1s" fill="freeze" begin="1h"/>
        </g>

        <!-- main sections -->
        <g stroke-width="20" fill="none" opacity="0">
          <animate id="show" attributeName="opacity" to="1" fill="freeze"
            dur=".1s" begin="1h"/>

          <g id="macro"></g>

          <g id="cursor" transform="rotate(90)">
            <line stroke-width="4" stroke-linecap="round" stroke="black"
              opacity=".9" y1="85" y2="115"/>
              <animateTransform attributeName="transform" type="rotate"
                repeatCount="1" by="180" fill="freeze" id="spin" begin="1h"/>
            <circle r="8" fill="black" opacity=".9" display="none"/>
          </g>
        </g>

        <!-- subsections -->
        <g id="micro"></g>

        <g stroke="none" id="scroll"></g>

        <g id="arrow" opacity="0">
          <animate id="show_arrow" attributeName="opacity" to="0.75" fill="freeze"
            dur=".1s" begin="1h"/>
          <animate id="hide_arrow" attributeName="opacity" to="0" fill="freeze"
            dur=".5s" begin="1h"/>
          <line stroke-width="2" stroke-linecap="round" stroke="black"
            opacity="1" x1="-110" x2="-85" y1="45" y2="45"/>
          <line stroke-width="2" stroke-linecap="round" stroke="black"
            opacity="1" x1="-90" x2="-85" y1="42" y2="45"/>
          <line stroke-width="2" stroke-linecap="round" stroke="black"
            opacity="1" x1="-90" x2="-85" y1="48" y2="45"/>
        </g>

      </g>

      <!-- Analysis data and SVG scripts -->
      <script>
        <![CDATA[

          var svg_template = document.getElementById('svg_template').innerHTML;

          var nestedData = [];

nestedData = { // Purcell Fairy Queen chaconne // demo
  "videoId":"Ct0GJqRRo_8","content":[
    [1.9,{"section":"","content":[
        [1.9,{"division":"Variation 1"}],
        [12.5,{"division":"Variation 2"}],
        [23.7,{"division":"Variation 3"}],
        [34.5,{"division":"Variation 4"}],
        [45.4,{"division":"Variation 5"}],
        [56.2,{"division":"Variation 6"}],
        [67,{"division":"Variation 7"}],
        [78.3,{"division":"Variation 8"}]
      ]}],
    [90,{"section":"Minore","content":[
        [90,{"division":"Variation 9"}],
        [101,{"division":"Variation 10"}]
      ]}],
    [112,{"section":"Majore","content":[
        [112,{"division":"Variation 11"}],
        [123.1,{"division":"Variation 12"}],
        [134.1,{"division":"Variation 13"}],
        [145.1,{"division":"Variation 14"}]
      ]}],
    [160,{"section":"","content":[
        [160,{"division":""}]
      ]}]
  ]
};

          // DOWNLOAD DATA

          function downloadObjectAsJson(exportObj, exportName) {
            // https://stackoverflow.com/questions/19721439/download-json-object-as-a-file-from-browser
            var dataStr = "data:text/json;charset=utf-8,"
              + encodeURIComponent(exportObj);
            var downloadAnchorNode = document.createElement('a');
            downloadAnchorNode.setAttribute("href",     dataStr);
            downloadAnchorNode.setAttribute("download", exportName + ".json");
            document.body.appendChild(downloadAnchorNode); // required for firefox
            downloadAnchorNode.click();
            downloadAnchorNode.remove();
          }

          // COMPUTATION

          function replaceData(newData) {
            nestedData = newData;
          }

          const preplay = 0; // seconds

          // following variables are not passed to the cursors (yet)
          const scope = 180;
          const origin = -90;

          var colors = ["#7dcffd", "#f69e70", "#fdd998", "#fc468e"];

          var greys = ["#c0c2ce", "#e5e6eb", "#afafaf", "#e5e6eb"];

          function polarToCartesian(centerX, centerY, radius, angleInDegrees) {
            var angleInRadians = (angleInDegrees-90) * Math.PI / 180.0;

            return {
              x: centerX + (radius * Math.cos(angleInRadians)),
              y: centerY + (radius * Math.sin(angleInRadians))
            };
          }

          function describeArc(x, y, radius, startAngle, endAngle){

              var start = polarToCartesian(x, y, radius, endAngle);
              var end = polarToCartesian(x, y, radius, startAngle);

              var largeArcFlag = endAngle - startAngle <= 180 ? "0" : "1";

              var d = [
                  "M", start.x, start.y, 
                  "A", radius, radius, 0, largeArcFlag, 0, end.x, end.y
              ].join(" ");

              return d;
          }

          function nonNeg(num) {
            return num < 0 ? 0 : num;
          }

          function drawForm() {

            // get data
            const nt = nestedData["content"]
              .map(sec => sec[1]["content"].map(div => div[0]));
            const nt_flat = nt.flat();

            // reload virgin template
            document.getElementById('svg_template').innerHTML = svg_template;

            // render text items
            let scroll = [];
            for (n = 0; n < nt.length; n++) {
              for (i = 0; i < nt[n].length; i++) {
                let cnode = nestedData["content"][n][1];
                scroll.push(
                  (i == 0 ? cnode["section"] + " " : "")
                  + (cnode["content"][i][1]["division"] == "" ? "" : "> ")
                  + cnode["content"][i][1]["division"]
                );
              }
            }
            let scroll_div = document.getElementById('scroll');
            let scroll_html = "";
            for (i = 0; i < scroll.length; i++) {
              let begin = preplay + nt_flat[i];
              scroll_html += '<text class="font_a" x="-75" y="'
              + (50+i*20).toString() + '"'
              // cue video
              + 'onclick="player.seekTo(' + begin.toString()
              + ');playSVG(' + (preplay + begin).toString() + ')">'
              + scroll[i].toString() + '</text>\n'
            }
            scroll_div.innerHTML = scroll_html;

            // render clock segments, macro
            let macro_div = document.getElementById('macro');
            let macro_html = "";
            for (n = 1; n <= nt.length; n++) { // each large section
              let temp_color = ["yellow", "red"][i % 2];
              macro_html += `
            <path id="sec` + n.toString() + `" stroke="` + temp_color + `" display="none"/>`
            }
            macro_html += `
          `;
            macro_div.innerHTML = macro_html;

            // render clock segments, micro
            let micro_div = document.getElementById('micro');
            let micro_html = "";
            for (n = 1; n <= nt.length; n++) { // each large section
              micro_html += `
        <g id="sec` + n.toString() + `-micro" stroke-width="20" fill="none" opacity="0">
          <animate id="show` + n.toString() + `" attributeName="opacity" to=".6" fill="freeze"
            dur=".1s" begin="1h"/>
          <animate id="hide` + n.toString() + `" attributeName="opacity" to="0" fill="freeze"
            dur=".4s" begin="1h"/>
`;
              for (i = 1; i <= nt[n-1].length; i++) { // each small section
                let temp_color = ["yellow", "red"][i % 2];
                micro_html += `
          <path id="sec` + n.toString() + `-` + i.toString() + `" stroke="` + temp_color + `" display="none"/>`;
              }
              micro_html += `

          <g id="cursor` + n.toString() + `" transform="rotate(90)" display="none">
            <line stroke-width="4" stroke-linecap="round" stroke="black"
              opacity="1" y1="-10" y2="65"/>
              <animateTransform attributeName="transform" type="rotate"
                repeatCount="1" by="180" fill="freeze" begin="1h" dur="1s"
                id="spin` + n.toString() + `"/>
            <circle r="6" fill="black" opacity="1"/>
          </g>
        </g>
`;
            }
            micro_div.innerHTML = micro_html;

            // animate arrow
            let arrow_div = document.getElementById('arrow');
            let arrow_translations = '';
            for (i = 1; i < scroll.length - 1; i++) {
              // assume final entry is for the ending
              let begin = preplay + nt_flat[i] - .2; // see dur value below
              arrow_translations += '<animateTransform attributeName="transform" \
                type="translate" repeatCount="1" fill="freeze" dur=".2s" \
                to="0 ' + (i*20).toString()
                + '" begin="' + begin.toString() + '"/>\n';
            }
            arrow_div.innerHTML = arrow_div.innerHTML + arrow_translations;

            if (nt[nt.length-1].length > 1) {
              // Add final entry for ending (missing due to user error)
              let last_subarray = nt[nt.length-1]
              let stop = 1 + last_subarray[last_subarray.length-1];
              nt.push([stop]);
            }
            for (i = 0; i < nt.length-1; i++) {
              // Copy start time of next large section into smaller array
              nt[i].push(nt[i+1][0]);
            }

            if (preplay < 0.8 && document.getElementById("preplaywrapper")) { // 0.4 plus time for the script to run
              document.getElementById("preplaywrapper").remove();
            } else if (document.getElementById("preplaywrapper")) {
              document.getElementById("showpreplay")
                .setAttribute("begin", 0.1);
              document.getElementById("preplay")
                .setAttribute("dur", nonNeg(preplay-0.1));
              document.getElementById("hidepreplay")
                .setAttribute("begin", nonNeg(preplay-0.4));
            }

            if (nt[0][0] < 0.1) {
              document.getElementById("readywrapper").remove();
            } else {
              document.getElementById("showready")
                .setAttribute("begin", preplay);
              document.getElementById("showready")
                .setAttribute("dur", 0.1);
              document.getElementById("ready")
                .setAttribute("begin", preplay);
              document.getElementById("ready")
                .setAttribute("dur", nt[0][0]);
              document.getElementById("ready")
                .setAttribute("end", preplay+nt[0][0]);
              document.getElementById("hideready")
                .setAttribute("begin", nonNeg(preplay+nt[0][0]-0.1));
            }

            document.getElementById("show_arrow")
              .setAttribute("begin", nonNeg(preplay+nt[0][0]));

            for (var n = 0; n < nt.length - 1; n++) {

              // draw macro arc segments

              var sec = document.getElementById("sec" + (n+1).toString());

              var start_arc = Math.round(
                origin + scope * (nt[n][0] - nt[0][0])
                / (nt.slice(-1)[0] - nt[0][0])
              );
              var end_arc = Math.round(
                origin + scope * (nt[n+1][0] - nt[0][0])
                / (nt.slice(-1)[0] - nt[0][0])
              );

              sec.setAttribute("d",
                describeArc(0, 0, 100, start_arc, end_arc+1));
              sec.removeAttribute("display");
              sec.setAttribute("stroke",
                colors[n % colors.length]);

              // cue video
              sec.setAttribute("onclick",
                "player.seekTo(" + nt[n][0] + ")"
                + ";"
                + "playSVG(" + (preplay + nt[n][0]) + ")"
              );

              for (var i = 0; i < nt[n].length - 1; i++) {

                // draw micro arc segments

                var start_arc = Math.round(
                  origin + scope
                  * (nt[n][i] - nt[n][0]) / (nt[n].slice(-1) - nt[n][0])
                );
                var end_arc = Math.round(
                  origin + scope
                  * (nt[n][i+1] - nt[n][0]) / (nt[n].slice(-1) - nt[n][0])
                );

                var sec = document.getElementById("sec" + (n+1).toString()
                  + "-" + (i+1).toString());

                sec.setAttribute("d",
                  describeArc(0, 0, 65, start_arc, end_arc+1));
                sec.removeAttribute("display");
                sec.setAttribute("stroke",
                  greys[(i + n % greys.length) % greys.length]);

                // cue video
                // problem with overlapping opaque divs
                // sec.setAttribute("onclick",
                //   "player.seekTo(" + nt[n][i] + ")"
                //   + ";"
                //   + "playSVG(" + nt[n][i] + ")"
                // );

              }

              // draw micro cursors and handle fades

              var cur = document.getElementById("cursor" + (n+1).toString());
              cur.removeAttribute("display");

              var spin = document.getElementById("spin" + (n+1).toString());
              spin.setAttribute("dur",nt[n+1][0] - nt[n][0]);
              spin.setAttribute("begin", preplay+nt[n][0]);

              var show = document.getElementById("show" + (n+1).toString());
              show.setAttribute("begin", nonNeg(preplay+nt[n][0]-0.1));

              var hide = document.getElementById("hide" + (n+1).toString());
              hide.setAttribute("begin", nonNeg(preplay+nt[n+1][0]-0.4));

            }

            // draw macro cursor and handle fade-in

            var spin = document.getElementById("spin");
            spin.setAttribute("dur", nt.slice(-1)[0] - nt[0][0]);
            spin.setAttribute("begin", preplay+nt[0][0]);

            var show = document.getElementById("show");
            show.setAttribute("begin", nonNeg(preplay+nt[0][0]-0.1));

            // fade-out arrow

            var hide = document.getElementById("hide_arrow");
            hide.setAttribute("begin", preplay+nt[nt.length-1][0]);

          }

        ]]>
      </script>

    </svg>

  </div>



  <div class="column">

    <!-- Youtube embed -->
    <div id="player"></div>

    <div>
      <button onclick="newYoutubeSelection()">New Youtube selection</button>
    </div>

  </div>



  <div class="column">

    <!-- Upload -->
    <div>
      <input style="margin-bottom:6px" type="file" id="selectFiles" value="Import" text="Select"/><br>
    </div>

    <!-- Buttons -->
    <div>
      <button onclick="uploadJson()" accessKey="u">Upload data</button>
      <button onclick="addEntry('large')" accessKey="l">New large section</button>
      <button onclick="addEntry('small')" accessKey="s">New small section</button>
      <button onclick="compileDataAndRender();playSVG();player.playVideo(0);">Preview SVG</button>
      <button onclick="compileDataAndRender(download=true)">Download data</button>
    </div>


    <!-- Table of timings and form descriptions (user input) -->
    <div>
      <table id="table" text-align="left">
        <tr>
          <th style="text-align:left">Time | </th>
          <th style="text-align:left">Edit</th>
          <th style="text-align:left"> | Description</th>
        </tr>
      </table>
    </div>



    <!-- Create analysis -->
    <script>

      var fs = 10; // fractions of a second

      function uploadJson() { // loads existing data into table if no file selected
        // https://stackoverflow.com/questions/36127648/uploading-a-json-file-and-using-it
        // adapted
        var files = document.getElementById('selectFiles').files;
        if (files.length <= 0) {
          loadTable();
          return false;
        }

        var fr = new FileReader();

        fr.onload = function(e) { 
          var result = JSON.parse(e.target.result);
          replaceData(result);
          loadTable();
          player.cueVideoById(result["videoId"]);

          renderSVG(result);
        }

        fr.readAsText(files.item(0));
      };

      // https://stackoverflow.com/a/46325093
      function deleteRow(r) {
        var i = r.parentNode.parentNode.rowIndex;
        document.getElementById("table").deleteRow(i);
      }

      function cueVideo(r) {
        entry = r.parentNode.parentNode.firstChild;
        stamp = Math.round(fs * Number(entry.innerHTML)) / fs;
        player.playVideo();
        player.seekTo(stamp);
      }

      function pushStamp(r,num = 0) {
        if (isNaN(num)) {
          return;
        }
        entry = r.parentNode.parentNode.firstChild;
        return entry.innerHTML = Math.round(
          fs * (Number(entry.innerHTML) + Number(num))
        ) / fs;
      }

      function redescribe(r) {
        entry = r.parentNode.parentNode.lastChild;
        let user_input = prompt("Describe", entry.innerHTML);
        return (user_input == null ? null : entry.innerHTML = user_input);
      }

      function loadTable() {

        let row_count = document.getElementById("table").rows.length;
        for (i = row_count - 1; i > 0; i--) {
          document.getElementById("table").deleteRow(i);
        }

        let nt = nestedData["content"]
          .map(sec => sec[1]["content"].map(div => div[0]));
        let nt_flat = nt.flat();
        let scroll = [];
        for (n = 0; n < nt.length; n++) {
          for (i = 0; i < nt[n].length; i++) {
            let cnode = nestedData["content"][n][1];
            scroll.push(
              (i == 0 ? cnode["section"] + " " : "")
              + (cnode["content"][i][1]["division"] == "" ? "" : "> ")
              + cnode["content"][i][1]["division"]
            );
          }
        }
        if (scroll.length != nt_flat.length) {
          return;
        }
        for (i = 0; i < scroll.length; i++) {
          addEntry(null, nt_flat[i], scroll[i]);
        }
      }

      // Change video
      function newYoutubeSelection() {
        let newVideoId = prompt("Enter new Youtube ID", "");
        player.cueVideoById(newVideoId);
      }
      
      // Create table
      function addEntry(scope=null, time=null, desc=null) {

        // to do: sort table

        let stamp = (time != null ? time
          : Math.round(fs * player.getCurrentTime()) / fs);

        var row = document.createElement("TR");
        var cellTime = document.createElement("TD");
        var cellCtrl = document.createElement("TD");
        var cellDesc = document.createElement("TD");
        cellDesc.className = "clickable";

        user_txt = (desc != null ? desc
          : prompt("Describe", ""));
        if (!user_txt) {
          user_txt = '';
        }
        var timenode = document.createTextNode(stamp.toString());
        if (scope == "small") {
          var descnode = document.createTextNode('> ' + user_txt);
        } else {
          var descnode = document.createTextNode('' + user_txt);
        }

        cellTime.appendChild(timenode);
        row.appendChild(cellTime);

        cellCtrl.innerHTML = ''
          + '<button onclick="deleteRow(this)">Delete</button>'
          + '<button onclick="pushStamp(this,'+ -1/fs +')">←</button>'
          + '<button onclick="cueVideo(this)">Cue video</button>'
          + '<button onclick="pushStamp(this,'+ 1/fs +')">→</button>'
          + '<button onclick="redescribe(this)">Redescribe</button>';
        row.appendChild(cellCtrl);

        cellDesc.appendChild(descnode);
        row.appendChild(cellDesc);

        document.getElementById("table").appendChild(row);
      }

      // Compile data
      function compileDataAndRender(download=false) {

        // Get user entries
        items = document.getElementById("table").rows;

        if (items.length <= 1) {
          return false;
        }

        var ta = [];
        for (i = 1; i < items.length; i++) {
          stamp = Number(items[i].cells[0].innerText
            || items[i].cells[0].textContent);
          description = items[i].cells[2].innerText
            || items[i].cells[2].textContent;
          ta.push([
            stamp,
            description
          ]);
        }
        ta = ta.sort( function(a, b){ return a[0]-b[0] } );

        // Parse data
        var newData = [];
        for (i = 0; i < ta.length; i++) {

          ds = ta[i][1].split(/ *> */);
          if (ds.length == 1) {
            // new large section
            newData.push([
              ta[i][0],
              {"section": ds[0], "content": [[ta[i][0], {"division": ""}]]}
            ]);
          } else if (ds.length == 2) {
            if (ds[0] != '') {
              // new large, small sections
              newData.push([
                ta[i][0],
                {"section": ds[0], "content": [[ta[i][0], {"division": ds[1]}]]}
              ]);
            } else if (ds[0] == '' && newData.length == 0) {
              // new small section; no large section to contain it yet
              newData.push([
                ta[i][0],
                {"section": "", "content": [[ta[i][0], {"division": ds[1]}]]}
              ]);
            } else {
              // new small section; will be nested under existing large section
              newData[newData.length-1][1]["content"].push([
                ta[i][0],
                {"division": ds[1]}
              ]);
            }
          }

        }


        // Provide data
        let videoId = player.getVideoData()['video_id'];
        let title = player.getVideoData()['title'];

        json_pretty = 'nestedData = {\n  "videoId": "'
          + videoId + '",\n  "Youtube title": "'
          + title + '",\n  "content":'
          + JSON.stringify(newData)
          .replace(/(\[[0-9.]+,{"division")/g, '\n        $1')
          .replace(/(\[[0-9.]+,{"section")/g, '\n    $1')
          .replace(/(]}])/g, '\n      $1')
          .replace(/(}])(])/g, '$1\n  $2')
          + '\n}'
        console.log(json_pretty);
        if (download) {
          downloadObjectAsJson(json_pretty.slice(13), videoId);
        }


        loadTable();
        renderSVG({
          "videoId":videoId,
          "Youtube title":title,
          "content":newData
        });

      }

    </script>


    <!-- Youtube embedding code (copied) -->
    <script>
      var videoId = nestedData["videoId"];

      var tag = document.createElement('script');

      tag.src = "https://www.youtube.com/iframe_api";
      var firstScriptTag = document.getElementsByTagName('script')[0];
      firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);

      var player;
      function onYouTubeIframeAPIReady() {
        player = new YT.Player('player', {
          height: '390',
          width: '640',
          videoId: videoId,
          events: {
            // 'onReady': onPlayerReady,
            'onStateChange': onPlayerStateChange
          }
        });
      }

      // function onPlayerReady(event) {
      //   event.target.playVideo();
      // }

      // function stopVideo() {
      //   player.stopVideo();
      // }

      function onPlayerStateChange(event) {
        if (event.data == YT.PlayerState.PLAYING) {
          let cue = Math.round(fs * player.getCurrentTime()) / fs;
          playSVG(cue);
        }
        if (event.data == YT.PlayerState.PAUSED
          || event.data == YT.PlayerState.BUFFERING) {
          pauseSVG();
        }
      }

    </script>

  </div>

</div>
</body>
</html>