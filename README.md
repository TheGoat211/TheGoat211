import random

class Slug:
    def __init__(self, name, element, power, experience=0):
        self.name = name
        self.element = element
        self.power = power
        self.experience = experience
        self.level = 1

    def attack(self):
        return self.power + random.randint(1, 5)

    def gain_experience(self, exp):
        self.experience += exp
        if self.experience >= 100:  # Chaque 100 exp, le slug monte de niveau
            self.level_up()

    def level_up(self):
        self.level += 1
        self.power += 5
        self.experience = 0
        print(f"{self.name} a évolué au niveau {self.level} ! Puissance augmentée à {self.power}.")

    def __str__(self):
        return f"{self.name} (Élément: {self.element}, Puissance: {self.power}, Niveau: {self.level})"

class Player:
    def __init__(self, name):
        self.name = name
        self.health = 100
        self.slugs = []

    def add_slug(self, slug):
        self.slugs.append(slug)

    def choose_slug(self):
        print(f"\n{self.name}, choisis un de tes slugs :")
        for i, slug in enumerate(self.slugs):
            print(f"{i + 1}. {slug}")
        choice = int(input("Choix du slug (1, 2, 3...): ")) - 1
        return self.slugs[choice]

    def take_damage(self, damage):
        self.health -= damage
        print(f"{self.name} subit {damage} dégâts ! Vie restante: {self.health}")

    def heal(self, amount):
        self.health += amount
        print(f"{self.name} récupère {amount} points de vie. Vie actuelle: {self.health}")

class Enemy:
    def __init__(self, name, health, element):
        self.name = name
        self.health = health
        self.element = element

    def take_damage(self, damage):
        self.health -= damage
        print(f"{self.name} subit {damage} dégâts ! Vie restante: {self.health}")

def get_element_multiplier(slug_element, enemy_element):
    """Détermine si un slug est fort ou faible contre un ennemi basé sur les éléments."""
    element_chart = {
        "Feu": {"fort": "Glace", "faible": "Eau"},
        "Eau": {"fort": "Feu", "faible": "Électricité"},
        "Électricité": {"fort": "Eau", "faible": "Terre"},
        "Terre": {"fort": "Électricité", "faible": "Feu"},
        "Glace": {"fort": "Terre", "faible": "Feu"}
    }
    
    if enemy_element == element_chart[slug_element]["fort"]:
        return 2  # Double dégâts
    elif enemy_element == element_chart[slug_element]["faible"]:
        return 0.5  # Moitié des dégâts
    else:
        return 1  # Dégâts normaux

def battle(player, enemy):
    print(f"\n{player.name} affronte {enemy.name} (Élément: {enemy.element}) !")
    while player.health > 0 and enemy.health > 0:
        slug = player.choose_slug()
        multiplier = get_element_multiplier(slug.element, enemy.element)
        player_attack = slug.attack() * multiplier
        print(f"Ton slug {slug.name} attaque avec une puissance de {player_attack:.1f} ! (Multiplicateur {multiplier}x)")
        enemy.take_damage(player_attack)
        
        if enemy.health <= 0:
            print(f"{enemy.name} est vaincu !")
            slug.gain_experience(50)  # Le slug gagne de l'expérience après une victoire
            break

        # L'ennemi contre-attaque
        enemy_attack = random.randint(5, 15)
        print(f"{enemy.name} contre-attaque avec une puissance de {enemy_attack} !")
        player.take_damage(enemy_attack)

    if player.health > 0:
        print(f"Félicitations {player.name}, tu as gagné !")
    else:
        print(f"{player.name} a été vaincu. Game Over.")

def explore_cavern(player, cavern):
    print(f"\nTu entres dans la {cavern['name']}...")
    events = ["trouver un slug rare", "être attaqué par un ennemi", "trouver un trésor", "rien ne se passe"]
    event = random.choice(events)
    
    if event == "trouver un slug rare":
        new_slug = Slug("Mystérieux Slug", "Ombre", random.randint(10, 20))
        print(f"Tu trouves un nouveau slug : {new_slug}")
        player.add_slug(new_slug)
    elif event == "être attaqué par un ennemi":
        enemy = random.choice(cavern['enemies'])
        print(f"Un ennemi apparaît : {enemy.name} (Élément: {enemy.element}) !")
        battle(player, enemy)
    elif event == "trouver un trésor":
        heal_amount = random.randint(10, 30)
        print(f"Tu trouves un trésor ! Il contient des objets de soin.")
        player.heal(heal_amount)
    else:
        print("Rien d'intéressant ne se passe...")

def show_world_map():
    print("\nCarte du monde :")
    return [
        {"name": "Caverne de Feu", "enemies": [Enemy("Lave Fiend", 60, "Feu")]},
        {"name": "Caverne Glacée", "enemies": [Enemy("Golem de Glace", 70, "Glace")]},
        {"name": "Caverne Aquatique", "enemies": [Enemy("Serpent d'Eau", 65, "Eau")]}
    ]

def main_game():
    print("Bienvenue dans Slugterra : Aventures Souterraines !")
    player_name = input("Entre ton nom : ")
    player = Player(player_name)
    
    # Ajout de slugs de départ
    slug1 = Slug("Infurnus", "Feu", 20)
    slug2 = Slug("Aquabeek", "Eau", 15)
    player.add_slug(slug1)
    player.add_slug(slug2)

    caverns = show_world_map()

    while player.health > 0:
        print("\nQue souhaites-tu faire ?
