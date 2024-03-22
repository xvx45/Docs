!!! warning "Ce site est en construction !"

    Ce site est actuellement en cours de construction.

    Pensez-vous pouvoir aider ? N'hésitez pas en cliquant sur la page avec un crayon à droite !

    Cela peut également être fait sur n'importe quelle page.
    
# BeamNG.drive Snippets

## Lua Code Snippets

### Dessiner un marqueur & détection de véhicules

Dessiner des marqueurs sur la carte peut être l'un des meilleurs moyens d'indiquer à l'utilisateur qu'il y a une forme d'interaction possible à cet endroit.

Dessiner un marqueur est assez simple. Voici un exemple de comment les marqueurs de l'itinéraire du bus est dessiné:

```Lua
  local function createBusMarker(markerName)
    local marker =  createObject('TSStatic')
    marker:setField('shapeName', 0, "art/shapes/interface/position_marker.dae")
    marker:setPosition(vec3(0, 0, 0))
    marker.scale = vec3(1, 1, 1)
    marker:setField('rotation', 0, '1 0 0 0')
    marker.useInstanceRenderData = true
    marker:setField('instanceColor', 0, '1 1 1 0')
    marker:setField('collisionType', 0, "Collision Mesh")
    marker:setField('decalType', 0, "Collision Mesh")
    marker:setField('playAmbient', 0, "1")
    marker:setField('allowPlayerStep', 0, "1")
    marker:setField('canSave', 0, "0")
    marker:setField('canSaveDynamicFields', 0, "1")
    marker:setField('renderNormals', 0, "0")
    marker:setField('meshCulling', 0, "0")
    marker:setField('originSort', 0, "0")
    marker:setField('forceDetail', 0, "-1")
    marker.canSave = false
    marker:registerObject(markerName)
    scenetree.MissionGroup:addObject(marker)
    return marker
  end

  -- Cela peut être appelé dans une boucle pour configurer vos marqueurs.. 
  -- NOTE: Vous devriez ne le faire qu'une seule fois dans votre configuration initiale et ne pas l'appeler à chaque frame.
  if #markers == 0 then
    for k,v in pairs(nameMarkers) do
      local mk = scenetree.findObject(v)
      if mk == nil then
        log('I', logTag,'Creating marker '..tostring(v))
        mk = createBusMarker(v)
        ScenarioObjectsGroup:addObject(mk.obj)
      end
      table.insert(markers, mk)
    end
  end
```

Voici un exemple de marqueur personnalisé provenant de [BeamNG-FuelStations](https://github.com/BeamMP/BeamNG-FuelStations/tree/master):

```Lua
  local stations = [
    { "location": [ -778.813,  485.973, 23.46 ], "type":"gas" },
    { "location": [  617.164, -192.107, 53.2  ], "type":"ev"  },
  ]

  local function IsEntityInsideArea(pos1, pos2, radius)
    return pos1:distance(pos2) < radius
  end

  local onUpdate = function (dt)
    for k, spot in pairs(stations) do -- Parcourir tous les emplacements sur la carte actuelle
      local bottomPos = vec3(spot.location[1], spot.location[2], spot.location[3])
      local topPos = bottomPos + vec3(0,0,2) -- Décaler le vecteur pour obtenir une position supérieure (2 mètres de hauteur).

      local spotInRange = false -- Ce point est-il dans la plage ? Utilisé pour la couleur
      local spotCompatible = false -- Ce point est-il compatible ?

      if activeVeh then -- Nous avons une voiture et elle est à nous (si en multijoueur)
        local vehPos = activeVeh:getPosition()

        spotInRange = IsEntityInsideArea(vec3(vehPos.x, vehPos.y,vehPos.z), bottomPos, 1.5)

        spotCompatible = activeFuelType == "any" or spot.type == "any" or activeFuelType == spot.type
      end

      local spotColor = (spotInRange and spotCompatible) and activeColorMap[spot.type] or inactiveColorMap[spot.type] or ColorF(1,1,1,0.5)

      debugDrawer:drawCylinder(bottomPos:toPoint3F(), topPos:toPoint3F(), 1, spotColor) --Bas, haut, rayon, couleur
    end
  end
```

### exemples de guihooks
#### Notifications Toast, en haut à droite de l'écran.
![image](https://github.com/StanleyDudek/Docs/assets/49531350/c8a87842-b95a-4eca-84dc-93072ecc9158)

```lua
--guihooks.trigger('toastrMsg', {type, title, msg, config = {timeOut}}) 
guihooks.trigger('toastrMsg', {type = "info", title = "Message d'information:", msg = "Message d'information : Texte ici", config = {timeOut = 5000}}) 
guihooks.trigger('toastrMsg', {type = "warning", title = "Message d'avertissement:", msg = "Message d'avertissement : Texte ici", config = {timeOut = 5000}}) 
guihooks.trigger('toastrMsg', {type = "error", title = "Message d'erreur:", msg = "Message d'erreur : Texte ici", config = {timeOut = 5000}}) 
```

#### Notifications, par défaut en haut à gauche de l'écran dans l'application Messages.
![image](https://github.com/StanleyDudek/Docs/assets/49531350/6baef813-50cb-43c3-9c59-0de550b014b6)

```lua
--guihooks.trigger('Message', {msg, ttl, category, icon}) --requires Messages app
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "arrow_upward", icon = "arrow_upward"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "arrow_downward", icon = "arrow_downward"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "flag", icon = "flag"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "check", icon = "check"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "check_circle", icon = "check_circle"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "warning", icon = "warning"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "error", icon = "error"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "directions_car", icon = "directions_car"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "star", icon = "star"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "timeline", icon = "timeline"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "save", icon = "save"}) 
guihooks.trigger('Message', {msg = "Message Text Here", ttl = 5.0, category = "settings", icon = "settings"}) 
```
#### Flash d'affichage centré, grand ou petit
![image](https://github.com/StanleyDudek/Docs/assets/49531350/d0cf754f-83f8-4d15-9159-27350da127de)
![image](https://github.com/StanleyDudek/Docs/assets/49531350/1df6fc9b-756f-484e-b8d9-5df346dc4c26)

```lua
--guihooks.trigger('ScenarioFlashMessage', {{msg, ttl, sound, big}} ) -- Nécessite l'application RaceCountdown UI
guihooks.trigger('ScenarioFlashMessage', {{"Message", 5.0, 0, true}} ) 
guihooks.trigger('ScenarioFlashMessage', {{"Message Text Here", 5.0, 0, false}} ) 

--Exemple de compte à rebours : lorsque tous sont exécutés en même temps, les éléments sont mis en file d'attente et suivront les uns après les autres une fois que le délai d'expiration précédent est écoulé
guihooks.trigger('ScenarioFlashMessage', {{"3", 1.0, "Engine.Audio.playOnce('AudioGui', 'event:UI_Countdown1')", true}}) 
guihooks.trigger('ScenarioFlashMessage', {{"2", 1.0, "Engine.Audio.playOnce('AudioGui', 'event:UI_Countdown2')", true}}) 
guihooks.trigger('ScenarioFlashMessage', {{"1", 1.0, "Engine.Audio.playOnce('AudioGui', 'event:UI_Countdown3')", true}}) 
guihooks.trigger('ScenarioFlashMessage', {{"GO!", 3.0, "Engine.Audio.playOnce('AudioGui', 'event:UI_CountdownGo')", true}}) 

--Un autre exemple de son
guihooks.trigger('ScenarioFlashMessage', {{"Teleported!", 3.0, "Engine.Audio.playOnce('AudioGui', 'event:UI_Checkpoint')", false}}) 
```

#### Affichage persistant de taille moyenne au centre
![image](https://github.com/StanleyDudek/Docs/assets/49531350/6290e018-6b3d-4674-98f2-34282a723258)
```lua
--guihooks.trigger('ScenarioRealtimeDisplay', {msg = msg} ) -- Nécessite l'application Race Realtime Display UI
guihooks.trigger('ScenarioRealtimeDisplay', {msg = "Message Text Here"} )
--Ces messages persistent et sont effacés avec une chaîne vide
--Si vous utilisez des données en temps réel, celui-ci est bon pour les mises à jour rapides (pensez aux chronomètres, aux calculs de distance, etc.).
guihooks.trigger('ScenarioRealtimeDisplay', {msg = ""} )
```

## IMGUI Code Snippets

## CEF UI Code Snippets
