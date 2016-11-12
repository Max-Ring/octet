ATTACK OF THE DUCKS

WITH REGARDS TO THE COMMITS ON GITHUB, THE 'ONE BIG COMMIT' WAS WHEN YOU FIXED MY OCTET AS I HAD ACCIDENTALLY CLONED FROM YOUR ACCOUNT, AND YOU HAD TO GO INTO THE CODE AND CHANGE WHERE IT WAS SOURCED FROM, AMONG OTHER THINGS. THIS IS JUST TO REMIND YOU OF THIS FACT.


Below is information about the changes made to Space Invaders to become Duck Invaders.

I added a function which would restart the game when the Enter key was pressed and released, at the Game Over screen.
    if (game_over && is_key_going_up(key_enter)) {
		  app_init();
		  return;
      
I changed the background colour and the border colour. I did this by changing the numbers in the below code.
  
  Border Colour:
   GLuint white = resource_dict::get_texture_handle(GL_RGB, "#ff0000");
      sprites[first_border_sprite+0].init(white, 0, -3, 6, 0.2f);
      sprites[first_border_sprite+1].init(white, 0,  3, 6, 0.2f);
      sprites[first_border_sprite+2].init(white, -3, 0, 0.2f, 6);
      sprites[first_border_sprite+3].init(white, 3,  0, 0.2f, 6);
      
  Background Colour:
    glClearColor(1, 0, 2, 1);
      glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);

As for the invading Ducks, I changed the file for the invaders as well as their sizing to allow them to fit together.
  
  GLuint invaderer = resource_dict::get_texture_handle(GL_RGBA, "assets/invaderers/Ducky.gif");
      for (int j = 0; j != num_rows; ++j) {
        for (int i = 0; i != num_cols; ++i) {
          assert(first_invaderer_sprite + i + j*num_cols <= last_invaderer_sprite);
          sprites[first_invaderer_sprite + i + j*num_cols].init(
            invaderer, ((float)i - num_cols * 0.5f) * 0.5f, 2.50f - ((float)j * 0.5f), 0.50f, 0.50f
      
I modified the code so that the Duck sprite would change texture to face the direction they were travelling.

sprite &border = sprites[first_border_sprite+(invader_velocity < 0 ? 2 : 3)];
      if (invaders_collide(border)) {
        invader_velocity = -invader_velocity;
        move_invaders(invader_velocity, -0.1f);
		if (invader_velocity < 0) {
			changeTexture("assets/invaderers/DuckyMoonWalk.gif");
		}
		else if (invader_velocity > 0) {
			changeTexture("assets/invaderers/Ducky.gif");	}
      }
    }
	void changeTexture(string file) {
		for (int i = first_invaderer_sprite; i <= last_invaderer_sprite; i++) {
			sprite &ducky = sprites[i];
			if (ducky.is_enabled()) {
				GLuint newTexture = resource_dict::get_texture_handle(GL_RGBA, file);
				ducky.replaceSpriteTexture(newTexture);
        
I wanted to have the missiles fire as if from a wing mounted launcher, so I modified the position of the missile and added another on
the other wing.
  for (int i = 0; i != num_missiles; ++i) {
			  if (!sprites[first_missile_sprite + i].is_enabled()) {
				  sprites[first_missile_sprite + i].set_relative(sprites[ship_sprite], 0.25f, 0.5f);
				  sprites[first_missile_sprite + i].is_enabled() = true;
				  missiles_disabled = 5;
				  ALuint source = get_sound_source();
				  alSourcei(source, AL_BUFFER, whoosh);
				  alSourcePlay(source);
				  break;
			  }
		  }
		  for (int i = 0; i != num_missiles; ++i) {
			  if (!sprites[first_missile_sprite + i].is_enabled()) {
				  sprites[first_missile_sprite + i].set_relative(sprites[ship_sprite], -0.25f, 0.5f);
				  sprites[first_missile_sprite + i].is_enabled() = true;
				  missiles_disabled = 5;
				  break;
          
I edited the movement of the ship to allow it to fly around the world space.

 if (is_key_down(key_up)) 
	  {
		  sprites[ship_sprite].translate(0, +ship_speed);
		  if (sprites[ship_sprite].collides_with(sprites[first_border_sprite + 1])) 
		  {
			  sprites[ship_sprite].translate(0, -ship_speed);
		  }
	  }
	  else if (is_key_down(key_down))
	  {
		  sprites[ship_sprite].translate(0, -ship_speed);
		  if (sprites[ship_sprite].collides_with(sprites[first_border_sprite + 0]))
		  {
			  sprites[ship_sprite].translate(0, +ship_speed);

I changed the number of missiles allowed to be spawned at any one time, as well as the number of invading ducks.
  
  enum {
      num_sound_sources = 12,
      num_rows = 5,
      num_cols = 10,
      num_missiles = 10,
      num_bombs = 10,
      num_borders = 4,
      num_invaderers = num_rows * num_cols,

I added a new source file for the sound that the Ducks would make when hit by a missile.

Quack = resource_dict::get_sound_handle(AL_FORMAT_MONO16, "assets/invaderers/Quack.wav");

void on_hit_invaderer() {
      ALuint source = get_sound_source();
      alSourcei(source, AL_BUFFER, Quack);
      alSourcePlay(source);


I recieved help from a several people, but the main two are Edward Burton and Delainey Ackerman.
