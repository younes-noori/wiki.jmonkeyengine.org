---
title: Monkey Zone: Multi-player Sample Project
---
<h1 class="sectionedit1" id="monkey_zonemulti-player_sample_project">Monkey Zone: Multi-player Sample Project</h1>
<div class="level1">

<p>
MonkeyZone is an multi-player demo game provided by the jME core developer team.
</p>
<ul>
<li class="level1"><div class="li"> <a href="http://monkeyzone.googlecode.com/svn/trunk/" class="urlextern" title="http://monkeyzone.googlecode.com/svn/trunk/" rel="nofollow">Download source code</a> (Subversion Repository)</div>
</li>
<li class="level1"><div class="li"> <a href="http://www.youtube.com/watch?v=98yITEoJvqE" class="urlextern" title="http://www.youtube.com/watch?v=98yITEoJvqE" rel="nofollow">Watch pre-alpha video footage</a> (YouTube Video)</div>
</li>
<li class="level1"><div class="li"> <a href="http://jmonkeyengine.org/2011/02/13/monkeyzone-a-jme3-game-from-the-core/" class="urlextern" title="http://jmonkeyengine.org/2011/02/13/monkeyzone-a-jme3-game-from-the-core/" rel="nofollow">Read "MonkeyZone – a jME3 game from the core"</a> (news article)</div>
</li>
<li class="level1"><div class="li"> <a href="http://jmonkeyengine.org/groups/user-code-projects/forum/topic/open-game-finder/" class="urlextern" title="http://jmonkeyengine.org/groups/user-code-projects/forum/topic/open-game-finder/" rel="nofollow">Related forum thread: Open Game Finder</a></div>
</li>
</ul>

<p>
This open-source demo:
</p>
<ol>
<li class="level1"><div class="li"> showcases one possible way to implement a game with jME3, and</div>
</li>
<li class="level1"><div class="li"> helps the jME team verify the jME3 <abbr title="Application Programming Interface">API</abbr> in terms of usability.</div>
</li>
</ol>

<p>
The game idea is based on “BattleZone” arcade game from the 1980s, a first-person shooter the with real-time strategy elements.
The game was written using the jMonkeyEngine SDK, and it's based off the BasicGame project template. It took us one week to create a playable pre-alpha, including networking.
The project design follows best practices that make it possible to edit maps, vehicles, etc, in jMonkeyEngine SDK without having to change the code – This allows 3D graphic designers to contribute models more easily. (If you feel like contributing assets or working on parts of the game code, drop us a note!)
</p>

</div>
<!-- EDIT1 SECTION "Monkey Zone: Multi-player Sample Project" [1-1376] -->
<h2 class="sectionedit2" id="implementation">Implementation</h2>
<div class="level2">

<p>
MonkeyZone is a multi-player game with a physics simulation. Both, clients and server, run the physics simulation. The clients send input data from the player group to the server, where they control the entities, and also broadcast to the clients. Additionally, the server sends regular syncronization data for all objects in the game to prevent drifting.
When a human user or an AI performs an action (presses a button), the actual logic is done on the server. The results are broadcast as data messages to the entities. When the entity is controlled by an AI, the actual AI code (that determines where the entity should move, and when it should perform an action) is executed on the client.
<br />
<br />

<em>The way MonkeyZone is implemented is just one of the many possible ways to do a game like this in jME. Some things might be done more efficiently, some might be done in another way completely. MonkeyZone tries to do things the way that are most appropriate to implement the game at hand and it shows nicely how jME3 and the standard Java <abbr title="Application Programming Interface">API</abbr> can make game development easier and faster. Also note that the way MonkeyZone is designed is not scalable to a MMO style game, it will only work in a FPS style environment where the whole game world can be loaded at once.</em>
</p>

</div>
<!-- EDIT2 SECTION "Implementation" [1377-2671] -->
<h3 class="sectionedit3" id="terminology">Terminology</h3>
<div class="level3">

<p>
The game uses certain terms that might be familiar to you but maybe used in another way, so heres a quick rundown on the terms being used.
</p>
<ul>
<li class="level1"><div class="li"> Player –	Logical human or AI player that can enter entities and generally act, only exists as PlayerData “database” with an id.</div>
</li>
<li class="level1"><div class="li"> Entity –	Spatial with UserData, a world object like character, vehicle, box or factory. The base form is defined only by a String pointing to the j3o which already has all userdata like hitpoints, speed etc.</div>
</li>
<li class="level1"><div class="li"> User –	Human player using a client</div>
</li>
<li class="level1"><div class="li"> Player Group – Group of players that play together (e.g. one human player and one AI companion per client). For now that's the same as client_id of human player for all AIControl’ed players originating from that client.</div>
</li>
<li class="level1"><div class="li"> Client –	Computer connected to server</div>
</li>
</ul>

</div>
<!-- EDIT3 SECTION "Terminology" [2672-3492] -->
<h3 class="sectionedit4" id="manager_classes">Manager Classes</h3>
<div class="level3">

<p>
The WorldManager does the main work of organizing players, entities and the world and synchronizing them between the server and client. Both client and server use this class. Some other managers like ClientEffectsManager only exist on the client or server and manage e.g. effects display.
The gameplay is largely controlled by the ServerGameManager which does gameplay logic on the server, combined with the actions issued by the AI and user on the client (see below) it implements the gameplay. It extensively uses the functions exposed by the WorldManager to perform actions and gather data. This is also the class where the actions of the players are actually executed on the server to determine the outcome (ray testing for shooting etc.).
</p>

</div>
<!-- EDIT4 SECTION "Manager Classes" [3493-4262] -->
<h3 class="sectionedit5" id="use_of_controls">Use of Controls</h3>
<div class="level3">

<p>
<a href="/jme3/advanced/custom_controls.html" class="wikilink1" title="jme3:advanced:custom_controls">Controls</a> are used extensively in MonkeyZone for many aspects of the game. When a player enters an entity, the Spatials Controls are configured based on the player that enters. For example when the human user enters an entity, Controls that update the user interface (DefaultHUDControl) or user input (UserInputControl) are added to the current entity Spatial.
</p>

</div>

<h4 id="as_entity_capabilities">...As entity capabilities</h4>
<div class="level4">

<p>
Controls attached to Spatials are generally used like an “array of capabilities” that the entity posesses. So when an entity has a VehicleControl its expected to be a vehicle, when its got a CharacterControl its expected to be a character.
Other Controls work completely on their own, like CharacterAnimControl which just uses the CharacterControl of the entity to check if the character is running, jumping etc. and then animates the entity if it has an AnimControl.
</p>

</div>

<h4 id="to_abstract">... to abstract</h4>
<div class="level4">

<p>
Furthermore theres special interfaces for Controls that allow abstraction of different Controls into one base interface. For example ManualControl and AutonomousControl are interfaces for controls that manage the movement of a spatial in a generalized way. This way AI code and e.g. the UserInputControl only have to check for a valid AutonomousControl or ManualControl on the spatial to control and move it. The details of the movement are handled by classes like ManualVehicleControl and AutonomousCharacterControl.
</p>

</div>

<h4 id="for_ai_functions">... for AI functions</h4>
<div class="level4">

<p>
A special Control called CommandControl handles the Commands that can be executed by user controlled players, see below.
</p>

</div>
<!-- EDIT5 SECTION "Use of Controls" [4263-5866] -->
<h3 class="sectionedit6" id="artificial_intelligence">Artificial Intelligence</h3>
<div class="level3">

<p>
MonkeyZone includes simple AI functions based on a command queue.
</p>

</div>

<h4 id="commands">Commands</h4>
<div class="level4">

<p>
To implement autonomous AI players MonkeyZone uses a system of Commands that are managed by a CommandControl that is attached to each AI player entity controlled by the user. This CommandControl manages a list of Commands that are executed based on priority. For example theres a MoveCommand, a FollowCommand and an AttackCommand, Commands can however implement more complete behavior than that, e.g. the complete logic for a scavenging entity.
</p>
<ul>
<li class="level1"><div class="li"> Press the WASD keys and use the mouse to move</div>
</li>
<li class="level1"><div class="li"> press space to jump</div>
</li>
<li class="level1"><div class="li"> Aim and click to shoot</div>
</li>
<li class="level1"><div class="li"> Type 1 to select the first Ogre</div>
<ul>
<li class="level2"><div class="li"> Aim at the floor and press F1 to tell it where to go.</div>
</li>
<li class="level2"><div class="li"> Aim at a target and press F2 to tell it who to follow.</div>
</li>
<li class="level2"><div class="li"> Aim at the car and press F3 to make it drive the car.</div>
</li>
<li class="level2"><div class="li"> Aim at a target and press F4 to tell it who to attack.</div>
</li>
<li class="level2"><div class="li"> Walk close to the car and press enter to drive the car.</div>
</li>
</ul>
</li>
</ul>

</div>

<h4 id="triggers">Triggers</h4>
<div class="level4">

<p>
The SphereTrigger is a TriggerControl that is also attached to each AI players current entity. It consists of a GhostControl that checks the overlapping entities around the entity its attached to. It can be assigned a command that is checked with every entity entering the SphereTrigger and executed if applicable (e.g. normal “attack enemy” mode).
</p>

</div>

<h4 id="navmesh">NavMesh</h4>
<div class="level4">

<p>
For each map a navigation mesh is generated that allows the entities to navigate the terrain. Autonomous entities automatically get a NavigationControl based on the current map. The AutonomousControl implementations automatically recognize the NavigationControl attached to the Spatial and use it for navigation. The NavMeshNavigationControl implementation contains a reference to the levels NavMesh and implements a navigation algorithm similar to the A* algorithm.
</p>

</div>
<!-- EDIT6 SECTION "Artificial Intelligence" [5867-7721] -->
<h3 class="sectionedit7" id="networking">Networking</h3>
<div class="level3">

<p>
Networking is realized in the PhysicsSyncManager which we hope to extend to a state where it can serve as a general sync system for physics based network games.
The sync manager basically puts a timestamp on every message sent from the server and then buffers all arriving messages on the client within a certain time window. This allows to compensate for messages arriving too soon or too late within the constraints of the buffer, a future version might step the clients physics space different to compensate for network delays without “snapping”.
</p>

</div>
<!-- EDIT7 SECTION "Networking" [7722-8296] -->
<h2 class="sectionedit8" id="use_of_jmonkeyengine_sdk_tools">Use of jMonkeyEngine SDK tools</h2>
<div class="level2">

<p>
All assets used in the game, like entity models and loaded maps can be preconfigured and edited using the jMonkeyEngine SDK. For example, to add a new vehicle type, a vehicle is created in the jMonkeyEngine SDK vehicle editor and UserData like Speed, HitPoints etc. is applied directly in the editor. When the model is loaded in the game it is automatically configured based on these settings, the same accounts for maps that are loaded, special Nodes that mark e.g. player start locations are recognized automatically etc.
</p>

</div>
<!-- EDIT8 SECTION "Use of jMonkeyEngine SDK tools" [8297-8863] -->
<h3 class="sectionedit9" id="userdata">UserData</h3>
<div class="level3">

<p>
Entities (Nodes and Geometries) that are loaded from disk have certain UserData like HitPoints, Speed etc. that is used to configure the entity at runtime. The jMonkeyEngine SDK allows adding and editing this UserData, so entity properties are editable visually.
</p>

</div>
<!-- EDIT9 SECTION "UserData" [8864-9145] -->
<h3 class="sectionedit10" id="physics">Physics</h3>
<div class="level3">

<p>
VehicleControls, CharacterControls and RigidBodyControls with mesh collision shape for terrain and objects are generated in the jMonkeyEngine SDK and saved in the entity j3o file. When an entity is loaded, the type of entity is identified based on the available controls and UserData and it is configured accordingly.
</p>

</div>
<!-- EDIT10 SECTION "Physics" [9146-9480] -->
<h2 class="sectionedit11" id="api_info">API Info</h2>
<div class="level2">

</div>
<!-- EDIT11 SECTION "API Info" [9481-9501] -->
<h3 class="sectionedit12" id="designer_infos">Designer Infos</h3>
<div class="level3">

<p>
Editable UserData of entity Spatials:
</p>
<ul>
<li class="level1"><div class="li"> (float) HitPoints</div>
</li>
<li class="level1"><div class="li"> (float) MaxHitPoints</div>
</li>
<li class="level1"><div class="li"> (float) Speed</div>
</li>
</ul>

<p>
Entity Spatial marking Node names:
</p>
<ul>
<li class="level1"><div class="li"> AimNode</div>
</li>
<li class="level1"><div class="li"> CameraAttachment</div>
</li>
<li class="level1"><div class="li"> ShootAttachment</div>
</li>
</ul>

<p>
Level Spatial marking Node names:
</p>
<ul>
<li class="level1"><div class="li"> StartPoint</div>
</li>
<li class="level1"><div class="li"> PowerSource</div>
</li>
<li class="level1"><div class="li"> MetalField</div>
</li>
</ul>

</div>

<h4 id="developer_infos">Developer Infos</h4>
<div class="level4">

<p>
Programmatic UserData of entities:
</p>
<ul>
<li class="level1"><div class="li"> (long) entity_id</div>
</li>
<li class="level1"><div class="li"> (int) group_id</div>
</li>
<li class="level1"><div class="li"> (long) player_id</div>
</li>
</ul>

<p>
Programmatic PlayerData:
</p>
<ul>
<li class="level1"><div class="li"> (long) id</div>
</li>
<li class="level1"><div class="li"> (int) group_id</div>
</li>
<li class="level1"><div class="li"> (long) entity_id</div>
</li>
<li class="level1"><div class="li"> (long) character_entity_id</div>
</li>
</ul>

</div>
<!-- EDIT12 SECTION "Designer Infos" [9502-10027] -->
<h2 class="sectionedit13" id="the_future">The Future</h2>
<div class="level2">

<p>
Have a look at the code and feel free to ask about it, if you want any new features, you are free to implement them. ;)
MonkeyZone is hosted at GoogleCode, where you can check out the jMonkeyEngine SDK-ready project via svn:
</p>
<ol>
<li class="level1"><div class="li"> jMonkeyEngine SDK→Team→Subversion→Checkout,</div>
</li>
<li class="level1"><div class="li"> Enter the SVN <abbr title="Uniform Resource Locator">URL</abbr> <code><a href="http://monkeyzone.googlecode.com/svn/trunk/" class="urlextern" title="http://monkeyzone.googlecode.com/svn/trunk/" rel="nofollow">http://monkeyzone.googlecode.com/svn/trunk/</a></code></div>
</li>
<li class="level1"><div class="li"> Download, open, and build the project</div>
</li>
<li class="level1"><div class="li"> Run the server first (com.jme3.monkeyzone.ServerMain), and then a client (com.jme3.monkeyzone.ClientMain).</div>
</li>
</ol>

</div>
<!-- EDIT13 SECTION "The Future" [10028-10550] -->
<h2 class="sectionedit14" id="troubleshooting">Troubleshooting</h2>
<div class="level2">
<ol>
<li class="level1"><div class="li"> After download, errors could appear because jme3tools.navmesh.util\NavMeshGenerator.java import com.jme3.terrain.Terrain is not known, you should correct this by setting Project Properties &gt; Libraries &gt; Add Library &gt; jme3-libraries-terrain </div>
</li>
</ol>
<div class="tags"><span>
	<a href="/tag/network.html" class="wikilink1" title="tag:network" rel="tag">network</a>,
	<a href="/tag/basegame.html" class="wikilink1" title="tag:basegame" rel="tag">basegame</a>,
	<a href="/tag/physics.html" class="wikilink1" title="tag:physics" rel="tag">physics</a>,
	<a href="/tag/inputs.html" class="wikilink1" title="tag:inputs" rel="tag">inputs</a>,
	<a href="/tag/spidermonkey.html" class="wikilink1" title="tag:spidermonkey" rel="tag">spidermonkey</a>
</span></div>

</div>
<!-- EDIT14 SECTION "Troubleshooting" [10551-] -->
