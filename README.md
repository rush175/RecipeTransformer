# RecipeTransformer

This python module takes in a URL pointing to a recipe from AllRecipes.com. It then promps the user to identify any and all supported 
transformations they would like to make to the recipe. These transformations can be any of the following 
	
	* To and from vegetarian and/or vegan
	* Style of cuisine
	* To and from healthy (or perhaps even different types of healthy)
	* DIY to easy
	* Cooking method (from bake to stir fry, for example)

The program then outputs a JSON representation of the new ingredients and changes made to the original recipe to accomplish these transformations. 

# Dependencies 
	* Used Python2.7 but works with Python3 as well. 
	* BeautifulSoup
	* NLTK
	* Requests


# Usage 

```python
# set URL variable to point to a AllRecipes.com url
URL = 'http://allrecipes.com/recipe/234667/chef-johns-creamy-mushroom-pasta/?internalSource=rotd&referringId=95&referringContentType=recipe%20hub'

# parse the URL to get relevant information
recipe_attrs = parse_url(URL) 		# parse_url returns a dict with data to populate a Recipe object
recipe = Recipe(**recipe_attrs)			# instantiate the Recipe object by unpacking dictionary

# apply a transformation
recipe_vegan = recipe.to_style('Mexican')	# convert the Creamy Mushroom Pasta to be Mexican style
recipe_vegan.print_pretty()				# print the new recipe 

```

# Methods

The following are the methods of the Recipe class

* to_vegan() - replaces non-vegan ingredients with vegan substitutes
* from_vegan() - replaces vegan substitutes with non-vegan ingredients like meat, dairy, ect. 
* to_vegetarian() - replaces non-vegetarian ingredients with vegan substitutes
* from_vegetarian() - replaces vegetarian substitutes with non-vegan ingredients like meat, dairy, ect. 
* to_style(style, threshold=0.9) - takes in a parameter of type string `style` (i.e. 'Mexican', 'Thai') and converts the recipe to be more of the input style. The parameter `threshold` allows the user to control how much they want their recipe changed to the desired style. Threshold is a float from 0.0 to 1.0 with 0.0 being no changes and 1.0 being as many changes as possible. 


# Classes

* **Recipe Class** is the main class in which all the transformation methods are. It also holds a list of Ingredient objects and Instruction objects parsed from the input recipe's URL (from allrecipes.com). It also finds the cooking tools and cooking methods used in the recipe by parsing the Instruction objects once they are instatiated and built. The Recipe class gets built by a dictionary object returned from `parse_url(URL)` function which scrapes the URL from allrecipes.com and returns a dictionary with all the necessary information to build the Recipe object. 

The Recipe class gets instatiated with a dictionary with the following schema:
<br />
{
	<br />
		name: string
		<br />
		preptime: int
		<br />
		cooktime: int
		<br />
		totaltime: int
		<br />
		ingredients: list of strings
		<br />
		instructions: list of strings
		<br />
		calories: int
		<br />
		carbs: int
		<br />
		fat: int
		<br />
		protien: int
		<br />
		cholesterol: int
		<br />
		sodium: int
<br />
}
<br />

and thus you can access information like `sodium` by calling recipe.sodium etc. The ingredients and instruction lists are instatiated and parsed in the Recipe's `__init__` method.


* **Ingredient Class** is used to parse and store the Ingredients in a clean and easily accessible manner. An Instruction object takes in a string (the text of a bullet point from the recipe's url in the ingredients section) and parses out the name, quantity, measurement, descriptor, preperation, and type. It does this using NLTK's part of speech tagger as well as a word bank. The type is a single letter correlated to one of the following:
<br />
		* H --> Herbs / Spices
		<br />
		* V --> Vegetable 
		<br />
		* M --> Meat
		<br />
		* D --> Dairy
		<br />
		* F --> Fruit
		<br />
		* S --> Sauce
		<br />
		* ? --> Misc.
		<br />

This is done by building out lists parsed from websites like wikipedia.com and naturalhealthtechniques.com that have long records for each category. By tagging each ingredient with a type, we are able to infer a lot more about the ingredient and it is integral to the Recipe's to_style method.


* **Instruction Class** is used to parse and store the Instructions in a clean and easily accessible manner. The `__init__` method calls other methods within the class to find the cooking methods and tools used in that instruction as well as the amount of time the instruction takes. It is important to store this in a different object than the Recipe class so that when we change the recipes, we can update the specific instrucitons and details accordingly.

