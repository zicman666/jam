extends Node2D

var ingredient = null
var erreur = 0
var erreur_max = 5
var je_peux_cuisiner = false	# vrai si le hero est à coté de l'écran de commande
var nb_ingredient_utilise_max = 5
var nb_ingredient_utilise = 0

var nb_salade
var nb_pain
var nb_seau_rempli
var nb_viande_crue

var nb_salade_utilise = 0
var nb_pain_utilise = 0
var nb_seau_rempli_utilise = 0
var nb_viande_crue_utilise = 0

var plat_tab=["boisson", "hamburger", "soupe", "viande"]
var plat = 0
var plat_max

onready var world = get_parent().get_parent()
onready var rupture_boisson = world.get_node("decors/menus/menu_boisson/cadre/rupture")
onready var rupture_hamburger = world.get_node("decors/menus/menu_hamburger/cadre/rupture")
onready var rupture_soupe = world.get_node("decors/menus/menu_soupe/cadre/rupture")
onready var rupture_viande = world.get_node("decors/menus/menu_viande/cadre/rupture")

var OUTIL = preload("res://scenes/outils.tscn")

func _ready():
	erreur_max = world.erreur_max
	z_index = position.y
	plat_max = plat_tab.size()-1
	
func _process(delta: float) -> void:
	$fleche_entree_anim.play("fleche_haut_bas") # indique ou mettre ingrédient
	$nb_ingredient/txt_ingredient_numero.text = "n°"+str(nb_ingredient_utilise)+"/4"
	
	_compte_ingredients()
	_recette_dispo_sur_panneau_affichage()
	_je_cuisine()
	_recette_selectionnee_sur_affichage_machine()
	
	if Input.is_action_just_pressed("lache"):
		world.state = "libre"
	
func _recette_dispo_sur_panneau_affichage():	#pain salade eau viande = vérif si rupture ou pas !?

	if _recette(0,0,4,0,-1): #boisson
		rupture_boisson.visible = false
	else:
		rupture_boisson.visible = true

	if _recette(2,1,0,1,-1): #hamburger
		rupture_hamburger.visible = false
	else:
		rupture_hamburger.visible = true

	if _recette(1,1,2,0,-1): #soupe
		rupture_soupe.visible = false
	else:
		rupture_soupe.visible = true

	if _recette(1,1,0,2,-1): #viande
		rupture_viande.visible = false
	else:
		rupture_viande.visible = true
		
func _recette_selectionnee_sur_affichage_machine():
	#suivant le "plat", affiche le sprite "plat" et "croix" si non dispo
	var p = "affiche_plat/"+str(plat_tab[plat]) 
	get_node(p).visible = true
	match plat_tab[plat]:
		"boisson":
			if rupture_boisson.visible == false:
				$affiche_plat/rupture.visible = false
				if Input.is_action_just_pressed("faire") and world.state == "je_cuisine":
					_recette(0,0,4,0,6)
			else:
				$affiche_plat/rupture.visible = true	
		"hamburger":
			if rupture_hamburger.visible == false:
				$affiche_plat/rupture.visible = false
				if Input.is_action_just_pressed("faire") and world.state == "je_cuisine":
					_recette(2,1,0,1,7)
			else:
				$affiche_plat/rupture.visible = true	
		"soupe":
			if rupture_soupe.visible == false:
				$affiche_plat/rupture.visible = false
				if Input.is_action_just_pressed("faire") and world.state == "je_cuisine":
					_recette(1,1,2,0,8)
			else:
				$affiche_plat/rupture.visible = true	
		"viande":
			if rupture_viande.visible == false:
				$affiche_plat/rupture.visible = false
				if Input.is_action_just_pressed("faire") and world.state == "je_cuisine":
					_recette(1,1,0,2,9)
			else:
				$affiche_plat/rupture.visible = true	

		
func _recette(a,b,c,d,id):	# vérif si recette en rupture ou pas : pain / salade / seau rempli / viande crue
							# id de plat cuisiné si recette validée (si id=-1 affichage rupture ou pas, si !=-1 on cuisine)
	if nb_pain>=a and nb_salade >= b and nb_seau_rempli >=c and nb_viande_crue >=d:
		if id !=-1:
			world.nb_pain -= a
			world.nb_salade -= b
			world.nb_seau_rempli -= c
			world.nb_viande_crue -= d
			_pop_plat(id)
		return true
	else:
		return false
				
func _je_cuisine():
	
	# cosmétique
	if world.state != "libre":
		$tapis_roulant.playing = true
		$anim_en_marche.play("en_marche")
		$anim_en_marche.playback_speed=2

	else:
		$tapis_roulant.playing = false			
		$anim_en_marche.stop()
		
	if Input.is_action_just_released("faire") and je_peux_cuisiner and world.state == "libre":
		world.state = "je_cuisine"
		
	if world.state == "je_cuisine":
		# sélection du plat à produire
		if Input.is_action_just_pressed("bas"):
			plat +=1
			if plat>plat_max:
				plat = 0
			$affiche_plat/boisson.visible = false
			$affiche_plat/hamburger.visible = false
			$affiche_plat/soupe.visible = false
			$affiche_plat/viande.visible = false

func _pop_plat(id):
	var outil = OUTIL.instance()
	outil.id = id
	outil.position.x = position.x
	outil.position.y = position.y + 30
	get_parent().add_child(outil)

		
func _compte_ingredients():	#COMPTE et AFFICHE le nombre d'ingrédient enregistré sur le WORLD
	
	nb_viande_crue = world.nb_viande_crue	
	$nb_ingredient/txt_nb_viande.text = str(world.nb_viande_crue)
		
	nb_salade = world.nb_salade		
	$nb_ingredient/txt_nb_salade.text = str(world.nb_salade	)
	
	nb_pain = world.nb_pain	
	$nb_ingredient/txt_nb_pain.text = str(world.nb_pain	)
	
	nb_seau_rempli = world.nb_seau_rempli	
	$nb_ingredient/txt_nb_seau_rempli.text = str(world.nb_seau_rempli	)	
	
	$nb_ingredient/txt_nb_erreur.text = str(erreur)
	
# commande de la machine
func _on_commande_area_entered(area: Area2D) -> void:
	if area.name == "aire_hero":
		je_peux_cuisiner = true
	
func _on_commande_area_exited(area: Area2D) -> void:
	if area.name == "aire_hero":
		je_peux_cuisiner = false
			
# le cuistot amène les ingrédient
func _on_entree_area_entered(area: Area2D) -> void:
	if area.name == "aire_hero":
		match area.get_parent().outil_en_main:
			2:
				area.get_parent().outil_en_main=1
				world.nb_seau_rempli += 1
			3:
				area.get_parent().outil_en_main = null
				world.nb_viande_crue += 1
			4:
				area.get_parent().outil_en_main = null
				world.nb_salade += 1
			10:
				area.get_parent().outil_en_main = null
				world.nb_pain += 1
