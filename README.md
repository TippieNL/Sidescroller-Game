# Sidescroller Run-and-Gun Starter Scaffold (GMS2/GML)

## 1) Asset Browser Tree (folders + asset names)
```
Rooms/
- rm_boot
- rm_stage_01
- rm_boss_01

Objects/
Core/
- obj_game
- obj_controller
- obj_camera
- obj_transition

Player/
- obj_player
- obj_player_muzzleflash

Weapons/
- obj_weapon_pickup (PARENT)
- obj_pickup_shotgun
- obj_pickup_cannon
- obj_bullet_player (PARENT)
- obj_bullet_enemy
- obj_grenade
- obj_explosion_hitbox

Enemies/
- obj_enemy_base (PARENT)
- obj_enemy_soldier
- obj_enemy_shield
- obj_enemy_turret
- obj_enemy_suicide

Boss/
- obj_boss_base
- obj_boss_phase_controller
- obj_boss_weakpoint

Level/
- obj_level_manager
- obj_spawn_controller
- obj_spawn_point
- obj_trigger_region
- obj_gate_lock
- obj_parallax_layer

World/
- obj_solid
- obj_ladder
- obj_hazard_spikes
- obj_destructible_prop

UI/
- obj_hud
- obj_pause_menu

FX/
- obj_fx_explosion
- obj_fx_smoke
- obj_fx_sparks

Scripts/
- scr_macros (use #macro constants)
- scr_globals_init

Core/
- scr_camera_init
- scr_camera_update
- scr_screen_shake
- scr_hitstop
- scr_fx_spawn
- scr_audio

Player/
- scr_player_create
- scr_player_step
- scr_player_move
- scr_player_jump
- scr_player_shoot
- scr_player_grenade
- scr_player_damage
- scr_player_anim

Weapons/
- scr_weapon_set
- scr_weapon_fire
- scr_weapon_ammo
- scr_damage_apply
- scr_projectile_step

Enemies/
- scr_enemy_base_create
- scr_enemy_base_step
- scr_enemy_damage
- scr_enemy_death
- scr_enemy_ai_helpers

Boss/
- scr_boss_init
- scr_boss_step
- scr_boss_phase_set
- scr_boss_attack_patterns
- scr_boss_death_sequence

Level/
- scr_room_setup
- scr_wave_load
- scr_wave_update
- scr_trigger_fire
- scr_parallax

UI/
- scr_hud_draw
- scr_ui_scaling

FX/
- scr_spawn_explosion
- scr_spawn_debris
```

## 2) Room setup details (rm_boot, rm_stage_01, rm_boss_01)

### rm_boot
- Instances: obj_game at (0,0)
- Layers:
  - Instances (default)

### rm_stage_01
- Layers (in this order):
  - Background
  - Parallax
  - Tiles_Back
  - Solids
  - Props
  - Enemies
  - Player
  - Bullets
  - FX
  - UI
- Room size example: 4096x180
- obj_level_manager and obj_spawn_controller created by scr_room_setup()

### rm_boss_01
- Layers (same as stage):
  - Background
  - Parallax
  - Tiles_Back
  - Solids
  - Props
  - Enemies
  - Player
  - Bullets
  - FX
  - UI
- Room size example: 1024x180
- Boss objects created by scr_room_setup() or placed manually

## 3) Objects (Create / Step / Draw)

### obj_game
**Create**
```gml
// Boot entry
scr_macros();
scr_globals_init();
instance_create_layer(0, 0, "Instances", obj_controller);
instance_create_layer(0, 0, "Instances", obj_camera);
room_goto(rm_stage_01);
```
**Step**
```gml
// no-op
```
**Draw**
```gml
// no-op
```

### obj_controller
**Create**
```gml
scr_room_setup();
```
**Step**
```gml
if (global.hitstop_frames > 0) {
    global.hitstop_frames -= 1;
    exit;
}
```
**Draw**
```gml
// no-op
```

### obj_camera
**Create**
```gml
scr_camera_init();
```
**Step**
```gml
scr_camera_update();
```
**Draw**
```gml
// no-op
```

### obj_transition
**Create**
```gml
alarm[0] = 30;
```
**Step**
```gml
// no-op
```
**Draw**
```gml
var a = 1 - (alarm[0] / 30);
draw_set_alpha(a);
draw_set_color(c_black);
draw_rectangle(0, 0, camera_get_view_width(view_camera[0]), camera_get_view_height(view_camera[0]), false);
draw_set_alpha(1);
```

### obj_player
**Create**
```gml
scr_player_create();
```
**Step**
```gml
scr_player_step();
```
**Draw**
```gml
scr_player_anim();
if (sprite_index != -1) {
    draw_self();
} else {
    draw_set_color(c_white);
    draw_rectangle(x-4, y-8, x+4, y+8, false);
}
```

### obj_player_muzzleflash
**Create**
```gml
image_alpha = 1;
life = 4;
```
**Step**
```gml
life -= 1;
if (life <= 0) instance_destroy();
```
**Draw**
```gml
if (sprite_index != -1) {
    draw_self();
} else {
    draw_set_color(c_yellow);
    draw_circle(x, y, 3, false);
}
```

### obj_weapon_pickup (PARENT)
**Create**
```gml
weapon_id = WEAPON_MACHINEGUN;
ammo = 30;
```
**Step**
```gml
if (place_meeting(x, y, obj_player)) {
    with (obj_player) {
        scr_weapon_set(other.weapon_id, other.ammo);
    }
    instance_destroy();
}
```
**Draw**
```gml
if (sprite_index != -1) {
    draw_self();
} else {
    draw_set_color(c_lime);
    draw_rectangle(x-4, y-4, x+4, y+4, false);
}
```

### obj_pickup_shotgun
**Create**
```gml
weapon_id = WEAPON_SHOTGUN;
ammo = 20;
```
**Step**
```gml
if (place_meeting(x, y, obj_player)) {
    with (obj_player) {
        scr_weapon_set(other.weapon_id, other.ammo);
    }
    instance_destroy();
}
```
**Draw**
```gml
if (sprite_index != -1) {
    draw_self();
} else {
    draw_set_color(c_orange);
    draw_rectangle(x-5, y-3, x+5, y+3, false);
}
```

### obj_pickup_cannon
**Create**
```gml
weapon_id = WEAPON_CANNON;
ammo = 10;
```
**Step**
```gml
if (place_meeting(x, y, obj_player)) {
    with (obj_player) {
        scr_weapon_set(other.weapon_id, other.ammo);
    }
    instance_destroy();
}
```
**Draw**
```gml
if (sprite_index != -1) {
    draw_self();
} else {
    draw_set_color(c_red);
    draw_rectangle(x-6, y-4, x+6, y+4, false);
}
```

### obj_bullet_player (PARENT)
**Create**
```gml
speed = 6;
direction = 0;
damage = 1;
life = 60;
knockback_x = 2;
knockback_y = -2;
```
**Step**
```gml
scr_projectile_step();
```
**Draw**
```gml
if (sprite_index != -1) {
    draw_self();
} else {
    draw_set_color(c_white);
    draw_rectangle(x-1, y-1, x+1, y+1, false);
}
```

### obj_bullet_enemy
**Create**
```gml
speed = 4;
direction = 180;
damage = 1;
life = 90;
knockback_x = -2;
knockback_y = -1;
```
**Step**
```gml
scr_projectile_step();
```
**Draw**
```gml
if (sprite_index != -1) {
    draw_self();
} else {
    draw_set_color(c_red);
    draw_rectangle(x-1, y-1, x+1, y+1, false);
}
```

### obj_grenade
**Create**
```gml
hsp = 3;
vsp = -5;
gravity = 0.25; // arc feel
life = 120;
```
**Step**
```gml
vsp += gravity;
x += hsp;
y += vsp;
if (place_meeting(x, y, obj_solid)) {
    vsp = -abs(vsp) * 0.6;
}
life -= 1;
if (life <= 0) {
    instance_create_layer(x, y, "FX", obj_explosion_hitbox);
    instance_destroy();
}
```
**Draw**
```gml
if (sprite_index != -1) {
    draw_self();
} else {
    draw_set_color(c_gray);
    draw_circle(x, y, 2, false);
}
```

### obj_explosion_hitbox
**Create**
```gml
radius = 24;
life = 4;
scr_screen_shake(4, 6);
```
**Step**
```gml
with (obj_enemy_base) {
    if (point_distance(x, y, other.x, other.y) < other.radius) {
        scr_damage_apply(other, id, 3, sign(other.x - x) * 4, -4);
    }
}
life -= 1;
if (life <= 0) instance_destroy();
```
**Draw**
```gml
draw_set_color(c_orange);
draw_circle(x, y, radius, false);
```

### obj_enemy_base (PARENT)
**Create**
```gml
scr_enemy_base_create();
```
**Step**
```gml
scr_enemy_base_step();
```
**Draw**
```gml
if (sprite_index != -1) {
    draw_self();
} else {
    draw_set_color(c_red);
    draw_rectangle(x-6, y-8, x+6, y+8, false);
}
```

### obj_enemy_soldier
**Create**
```gml
scr_enemy_base_create();
shoot_timer = irandom_range(30, 90);
```
**Step**
```gml
scr_enemy_base_step();
shoot_timer -= 1;
if (shoot_timer <= 0) {
    var b = instance_create_layer(x, y-4, "Bullets", obj_bullet_enemy);
    b.direction = (obj_player.x > x) ? 0 : 180;
    shoot_timer = irandom_range(45, 120);
}
```
**Draw**
```gml
if (sprite_index != -1) draw_self(); else draw_rectangle(x-5, y-7, x+5, y+7, false);
```

### obj_enemy_shield
**Create**
```gml
scr_enemy_base_create();
shield_dir = 0; // faces right
```
**Step**
```gml
scr_enemy_base_step();
```
**Draw**
```gml
if (sprite_index != -1) draw_self(); else draw_rectangle(x-6, y-8, x+6, y+8, false);
```

### obj_enemy_turret
**Create**
```gml
scr_enemy_base_create();
shoot_timer = 60;
```
**Step**
```gml
scr_enemy_base_step();
shoot_timer -= 1;
if (shoot_timer <= 0) {
    var b = instance_create_layer(x, y-6, "Bullets", obj_bullet_enemy);
    b.direction = point_direction(x, y, obj_player.x, obj_player.y);
    shoot_timer = 60;
}
```
**Draw**
```gml
if (sprite_index != -1) draw_self(); else draw_rectangle(x-6, y-6, x+6, y+6, false);
```

### obj_enemy_suicide
**Create**
```gml
scr_enemy_base_create();
```
**Step**
```gml
scr_enemy_base_step();
if (point_distance(x, y, obj_player.x, obj_player.y) < 24) {
    instance_create_layer(x, y, "FX", obj_explosion_hitbox);
    instance_destroy();
}
```
**Draw**
```gml
if (sprite_index != -1) draw_self(); else draw_rectangle(x-4, y-4, x+4, y+4, false);
```

### obj_boss_base
**Create**
```gml
scr_boss_init();
```
**Step**
```gml
scr_boss_step();
```
**Draw**
```gml
if (sprite_index != -1) draw_self(); else draw_rectangle(x-24, y-16, x+24, y+16, false);
```

### obj_boss_phase_controller
**Create**
```gml
phase = 0;
phase_timer = 120;
scr_boss_phase_set(phase);
```
**Step**
```gml
phase_timer -= 1;
if (phase_timer <= 0) {
    phase = (phase + 1) mod 3;
    scr_boss_phase_set(phase);
}
```
**Draw**
```gml
// no-op
```

### obj_boss_weakpoint
**Create**
```gml
hp = 10;
```
**Step**
```gml
if (hp <= 0) instance_destroy();
```
**Draw**
```gml
if (sprite_index != -1) draw_self(); else draw_circle(x, y, 6, false);
```

### obj_level_manager
**Create**
```gml
stage_id = 1;
waves = scr_wave_load(stage_id);
wave_index = 0;
```
**Step**
```gml
scr_wave_update(waves, wave_index);
```
**Draw**
```gml
// no-op
```

### obj_spawn_controller
**Create**
```gml
active = true;
```
**Step**
```gml
// no-op
```
**Draw**
```gml
// no-op
```

### obj_spawn_point
**Create**
```gml
enemy_type = ENEMY_SOLDIER;
```
**Step**
```gml
if (point_distance(x, y, obj_player.x, obj_player.y) < 200) {
    scr_trigger_fire(enemy_type, x, y);
    instance_destroy();
}
```
**Draw**
```gml
draw_set_color(c_green);
draw_rectangle(x-3, y-3, x+3, y+3, false);
```

### obj_trigger_region
**Create**
```gml
triggered = false;
```
**Step**
```gml
if (!triggered && place_meeting(x, y, obj_player)) {
    triggered = true;
    scr_trigger_fire(ENEMY_SOLDIER, x+32, y);
}
```
**Draw**
```gml
draw_set_color(c_blue);
draw_rectangle(bbox_left, bbox_top, bbox_right, bbox_bottom, false);
```

### obj_gate_lock
**Create**
```gml
locked = true;
```
**Step**
```gml
if (locked && instance_number(obj_enemy_base) == 0) locked = false;
```
**Draw**
```gml
if (locked) {
    draw_set_color(c_black);
    draw_rectangle(x-8, y-16, x+8, y+16, false);
}
```

### obj_parallax_layer
**Create**
```gml
scroll_factor = 0.5;
```
**Step**
```gml
// no-op
```
**Draw**
```gml
// placeholder simple parallax block
var cam = view_camera[0];
var vx = camera_get_view_x(cam);
draw_set_color(c_dkgray);
draw_rectangle(vx * scroll_factor, 0, vx * scroll_factor + 320, 180, false);
```

### obj_solid
**Create**
```gml
// no-op
```
**Step**
```gml
// no-op
```
**Draw**
```gml
if (sprite_index != -1) draw_self(); else draw_rectangle(bbox_left, bbox_top, bbox_right, bbox_bottom, false);
```

### obj_ladder
**Create**
```gml
// no-op
```
**Step**
```gml
// no-op
```
**Draw**
```gml
draw_set_color(c_yellow);
draw_rectangle(bbox_left, bbox_top, bbox_right, bbox_bottom, false);
```

### obj_hazard_spikes
**Create**
```gml
// no-op
```
**Step**
```gml
if (place_meeting(x, y, obj_player)) {
    with (obj_player) { scr_player_damage(1, 2, -4); }
}
```
**Draw**
```gml
draw_set_color(c_red);
draw_triangle(x-8, y, x, y-6, x+8, y, false);
```

### obj_destructible_prop
**Create**
```gml
hp = 3;
```
**Step**
```gml
if (hp <= 0) {
    scr_spawn_debris(x, y);
    instance_destroy();
}
```
**Draw**
```gml
if (sprite_index != -1) draw_self(); else draw_rectangle(x-6, y-6, x+6, y+6, false);
```

### obj_hud
**Create**
```gml
// no-op
```
**Step**
```gml
// no-op
```
**Draw**
```gml
scr_hud_draw();
```

### obj_pause_menu
**Create**
```gml
paused = false;
```
**Step**
```gml
if (keyboard_check_pressed(vk_escape)) paused = !paused;
```
**Draw**
```gml
if (paused) {
    draw_set_color(c_black);
    draw_text(8, 8, "PAUSED");
}
```

### obj_fx_explosion
**Create**
```gml
life = 10;
```
**Step**
```gml
life -= 1;
if (life <= 0) instance_destroy();
```
**Draw**
```gml
draw_set_color(c_orange);
draw_circle(x, y, 8 - life, false);
```

### obj_fx_smoke
**Create**
```gml
life = 20;
```
**Step**
```gml
life -= 1;
if (life <= 0) instance_destroy();
```
**Draw**
```gml
draw_set_color(c_gray);
draw_circle(x, y, 3 + life * 0.1, false);
```

### obj_fx_sparks
**Create**
```gml
life = 6;
```
**Step**
```gml
life -= 1;
if (life <= 0) instance_destroy();
```
**Draw**
```gml
draw_set_color(c_yellow);
draw_line(x-2, y-2, x+2, y+2);
```

## 4) Scripts (full GML function code)

### scr_macros
```gml
function scr_macros() {
    #macro GAME_W 320
    #macro GAME_H 180
    #macro WEAPON_MACHINEGUN 0
    #macro WEAPON_SHOTGUN 1
    #macro WEAPON_CANNON 2
    #macro ENEMY_SOLDIER 0
    #macro ENEMY_SHIELD 1
    #macro ENEMY_TURRET 2
    #macro ENEMY_SUICIDE 3
}
```

### scr_globals_init
```gml
function scr_globals_init() {
    global.score = 0;
    global.hitstop_frames = 0;
    global.shake_time = 0;
    global.shake_mag = 0;
    global.current_weapon = WEAPON_MACHINEGUN;
}
```

### scr_camera_init
```gml
function scr_camera_init() {
    camera = camera_create_view(0, 0, GAME_W, GAME_H, 0, -1, -1, -1, -1);
    view_camera[0] = camera;
    view_visible[0] = true;
    view_enabled = true;
    lookahead = 24;
}
```

### scr_camera_update
```gml
function scr_camera_update() {
    if (!instance_exists(obj_player)) exit;
    var target_x = obj_player.x + (obj_player.facing * lookahead) - GAME_W * 0.5;
    var target_y = obj_player.y - GAME_H * 0.5;
    target_x = clamp(target_x, 0, room_width - GAME_W);
    target_y = clamp(target_y, 0, room_height - GAME_H);

    if (global.shake_time > 0) {
        global.shake_time -= 1;
        target_x += irandom_range(-global.shake_mag, global.shake_mag);
        target_y += irandom_range(-global.shake_mag, global.shake_mag);
    }

    camera_set_view_pos(view_camera[0], target_x, target_y);
}
```

### scr_screen_shake
```gml
function scr_screen_shake(mag, time) {
    global.shake_mag = mag;
    global.shake_time = time;
}
```

### scr_hitstop
```gml
function scr_hitstop(frames) {
    global.hitstop_frames = max(global.hitstop_frames, frames);
}
```

### scr_fx_spawn
```gml
function scr_fx_spawn(_obj, _x, _y, _layer) {
    return instance_create_layer(_x, _y, _layer, _obj);
}
```

### scr_audio
```gml
function scr_audio(_name) {
    // placeholder for future audio routing
}
```

### scr_player_create
```gml
function scr_player_create() {
    hsp = 0;
    vsp = 0;
    accel = 0.5; // feel tuning
    max_spd = 2.5;
    gravity = 0.3; // feel tuning
    jump_spd = -5.5; // feel tuning
    coyote_time = 6;
    coyote_timer = 0;
    jump_buffer = 6;
    jump_buffer_timer = 0;
    on_ground = false;
    facing = 1;
    invuln = 0;
    hp = 5;
    grenades = 3;
    weapon_id = WEAPON_MACHINEGUN;
    weapon_ammo = -1;
    fire_timer = 0;
}
```

### scr_player_step
```gml
function scr_player_step() {
    scr_player_move();
    scr_player_jump();
    scr_player_shoot();
    scr_player_grenade();
    if (invuln > 0) invuln -= 1;
}
```

### scr_player_move
```gml
function scr_player_move() {
    var move = keyboard_check(ord("D")) - keyboard_check(ord("A"));
    hsp = clamp(hsp + move * accel, -max_spd, max_spd);
    if (move == 0) hsp = lerp(hsp, 0, 0.2);

    if (move != 0) facing = move;

    vsp += gravity;

    on_ground = place_meeting(x, y+1, obj_solid);
    if (on_ground) {
        coyote_timer = coyote_time;
    } else {
        coyote_timer = max(0, coyote_timer - 1);
    }

    x += hsp;
    if (place_meeting(x, y, obj_solid)) {
        x -= hsp;
        hsp = 0;
    }

    y += vsp;
    if (place_meeting(x, y, obj_solid)) {
        y -= vsp;
        vsp = 0;
    }
}
```

### scr_player_jump
```gml
function scr_player_jump() {
    if (keyboard_check_pressed(vk_space)) jump_buffer_timer = jump_buffer;
    if (jump_buffer_timer > 0) jump_buffer_timer -= 1;

    if (jump_buffer_timer > 0 && coyote_timer > 0) {
        vsp = jump_spd;
        coyote_timer = 0;
        jump_buffer_timer = 0;
    }
}
```

### scr_player_shoot
```gml
function scr_player_shoot() {
    if (fire_timer > 0) fire_timer -= 1;
    if (mouse_check_button(mb_left) && fire_timer <= 0) {
        var aim_up = keyboard_check(vk_up);
        scr_weapon_fire(weapon_id, aim_up);
        fire_timer = (weapon_id == WEAPON_MACHINEGUN) ? 6 : 12; // feel tuning
    }
}
```

### scr_player_grenade
```gml
function scr_player_grenade() {
    if (keyboard_check_pressed(ord("G")) && grenades > 0) {
        var g = instance_create_layer(x, y-4, "Bullets", obj_grenade);
        g.hsp = facing * 3;
        grenades -= 1;
    }
}
```

### scr_player_damage
```gml
function scr_player_damage(amount, kb_x, kb_y) {
    if (invuln > 0) exit;
    hp -= amount;
    hsp = kb_x;
    vsp = kb_y;
    invuln = 60;
    scr_hitstop(4);
}
```

### scr_player_anim
```gml
function scr_player_anim() {
    // placeholder for sprite switching
}
```

### scr_weapon_set
```gml
function scr_weapon_set(_weapon_id, _ammo) {
    weapon_id = _weapon_id;
    weapon_ammo = _ammo;
}
```

### scr_weapon_fire
```gml
function scr_weapon_fire(_weapon_id, _aim_up) {
    var dir = _aim_up ? 270 : (facing == 1 ? 0 : 180);
    var b = instance_create_layer(x + facing * 6, y-4, "Bullets", obj_bullet_player);
    b.direction = dir;
    b.speed = 6;
    if (_weapon_id == WEAPON_SHOTGUN) {
        for (var i = -1; i <= 1; i++) {
            var s = instance_create_layer(x + facing * 6, y-4, "Bullets", obj_bullet_player);
            s.direction = dir + i * 10;
            s.speed = 5;
        }
    } else if (_weapon_id == WEAPON_CANNON) {
        b.speed = 4;
        b.damage = 3;
    }

    if (_weapon_id != WEAPON_MACHINEGUN) {
        weapon_ammo -= 1;
        if (weapon_ammo <= 0) {
            weapon_id = WEAPON_MACHINEGUN;
            weapon_ammo = -1;
        }
    }

    instance_create_layer(x + facing * 8, y-4, "FX", obj_player_muzzleflash);
}
```

### scr_weapon_ammo
```gml
function scr_weapon_ammo() {
    return weapon_ammo;
}
```

### scr_damage_apply
```gml
function scr_damage_apply(attacker, target, amount, knockback_x, knockback_y) {
    if (instance_exists(target)) {
        if (target.object_index == obj_player) {
            with (target) scr_player_damage(amount, knockback_x, knockback_y);
        } else if (target.object_index == obj_enemy_base || target.parent == obj_enemy_base) {
            with (target) scr_enemy_damage(amount, knockback_x, knockback_y);
        }
    }
}
```

### scr_projectile_step
```gml
function scr_projectile_step() {
    x += lengthdir_x(speed, direction);
    y += lengthdir_y(speed, direction);
    life -= 1;

    if (place_meeting(x, y, obj_solid) || life <= 0) {
        instance_destroy();
        exit;
    }

    if (object_index == obj_bullet_player) {
        var hit = instance_place(x, y, obj_enemy_base);
        if (hit != noone) {
            scr_damage_apply(id, hit, damage, knockback_x, knockback_y);
            instance_destroy();
        }
    } else {
        if (place_meeting(x, y, obj_player)) {
            scr_damage_apply(id, obj_player, damage, knockback_x, knockback_y);
            instance_destroy();
        }
    }
}
```

### scr_enemy_base_create
```gml
function scr_enemy_base_create() {
    hp = 3;
    hurt_timer = 0;
    radius = 8;
    hsp = -0.5;
}
```

### scr_enemy_base_step
```gml
function scr_enemy_base_step() {
    if (hurt_timer > 0) hurt_timer -= 1;
    x += hsp;
    if (place_meeting(x, y, obj_solid)) {
        hsp = -hsp;
    }
    if (hp <= 0) scr_enemy_death();
}
```

### scr_enemy_damage
```gml
function scr_enemy_damage(amount, kb_x, kb_y) {
    hp -= amount;
    hurt_timer = 6;
    hsp = kb_x;
    scr_hitstop(2);
}
```

### scr_enemy_death
```gml
function scr_enemy_death() {
    scr_spawn_explosion(x, y);
    global.score += 100;
    instance_destroy();
}
```

### scr_enemy_ai_helpers
```gml
function scr_enemy_ai_helpers() {
    // placeholder for line-of-sight or ranges
}
```

### scr_boss_init
```gml
function scr_boss_init() {
    hp = 50;
    attack_timer = 60;
}
```

### scr_boss_step
```gml
function scr_boss_step() {
    scr_boss_attack_patterns();
    if (hp <= 0) scr_boss_death_sequence();
}
```

### scr_boss_phase_set
```gml
function scr_boss_phase_set(phase) {
    switch (phase) {
        case 0: attack_timer = 60; break;
        case 1: attack_timer = 45; break;
        case 2: attack_timer = 30; break;
    }
}
```

### scr_boss_attack_patterns
```gml
function scr_boss_attack_patterns() {
    attack_timer -= 1;
    if (attack_timer <= 0) {
        // bullets
        for (var i = 0; i < 4; i++) {
            var b = instance_create_layer(x, y, "Bullets", obj_bullet_enemy);
            b.direction = i * 90;
        }
        // slam
        scr_screen_shake(6, 10);
        // minion
        instance_create_layer(x-24, y, "Enemies", obj_enemy_soldier);
        attack_timer = 60;
    }
}
```

### scr_boss_death_sequence
```gml
function scr_boss_death_sequence() {
    for (var i = 0; i < 3; i++) {
        scr_spawn_explosion(x + irandom_range(-16, 16), y + irandom_range(-8, 8));
    }
    room_goto(rm_stage_01);
}
```

### scr_room_setup
```gml
function scr_room_setup() {
    var layers = ["UI","FX","Player","Enemies","Bullets","Props","Solids","Tiles_Back","Parallax","Background"];
    for (var i = 0; i < array_length(layers); i++) {
        if (!layer_exists(layers[i])) layer_create(layers[i]);
    }

    if (!instance_exists(obj_level_manager)) instance_create_layer(0, 0, "Instances", obj_level_manager);
    if (!instance_exists(obj_spawn_controller)) instance_create_layer(0, 0, "Instances", obj_spawn_controller);
    if (!instance_exists(obj_hud)) instance_create_layer(0, 0, "UI", obj_hud);

    if (!instance_exists(obj_player)) instance_create_layer(32, 120, "Player", obj_player);
}
```

### scr_wave_load
```gml
function scr_wave_load(stage_id) {
    var waves = [];
    if (stage_id == 1) {
        waves[0] = {x: 200, type: ENEMY_SOLDIER};
        waves[1] = {x: 320, type: ENEMY_TURRET};
        waves[2] = {x: 480, type: ENEMY_SUICIDE};
    }
    return waves;
}
```

### scr_wave_update
```gml
function scr_wave_update(waves, wave_index) {
    if (!instance_exists(obj_player)) exit;
    if (wave_index >= array_length(waves)) exit;
    var cam_x = obj_player.x;
    var w = waves[wave_index];
    if (cam_x > w.x) {
        scr_trigger_fire(w.type, w.x, obj_player.y);
        with (obj_level_manager) wave_index += 1;
    }
}
```

### scr_trigger_fire
```gml
function scr_trigger_fire(enemy_type, sx, sy) {
    switch (enemy_type) {
        case ENEMY_SOLDIER: instance_create_layer(sx, sy, "Enemies", obj_enemy_soldier); break;
        case ENEMY_SHIELD: instance_create_layer(sx, sy, "Enemies", obj_enemy_shield); break;
        case ENEMY_TURRET: instance_create_layer(sx, sy, "Enemies", obj_enemy_turret); break;
        case ENEMY_SUICIDE: instance_create_layer(sx, sy, "Enemies", obj_enemy_suicide); break;
    }
}
```

### scr_parallax
```gml
function scr_parallax() {
    // placeholder for future parallax system
}
```

### scr_hud_draw
```gml
function scr_hud_draw() {
    var cam = view_camera[0];
    var vx = camera_get_view_x(cam);
    var vy = camera_get_view_y(cam);
    draw_set_color(c_white);
    draw_text(vx + 8, vy + 8, "HP: " + string(obj_player.hp));
    draw_text(vx + 8, vy + 20, "Grenades: " + string(obj_player.grenades));
    draw_text(vx + 8, vy + 32, "Weapon: " + string(obj_player.weapon_id));
    draw_text(vx + 8, vy + 44, "Ammo: " + string(obj_player.weapon_ammo));
    draw_text(vx + 8, vy + 56, "Score: " + string(global.score));
}
```

### scr_ui_scaling
```gml
function scr_ui_scaling() {
    display_set_gui_size(GAME_W, GAME_H);
}
```

### scr_spawn_explosion
```gml
function scr_spawn_explosion(_x, _y) {
    scr_fx_spawn(obj_fx_explosion, _x, _y, "FX");
    scr_screen_shake(3, 6);
}
```

### scr_spawn_debris
```gml
function scr_spawn_debris(_x, _y) {
    scr_fx_spawn(obj_fx_smoke, _x, _y, "FX");
}
```

## 5) Wiring Checklist
1. Create all folders and assets exactly as listed in the Asset Browser Tree.
2. Create rooms:
   - rm_boot: place obj_game at (0,0) on default Instances layer.
   - rm_stage_01: ensure layers exist in the specified order; place a few obj_solid platforms on Solids.
   - rm_boss_01: ensure layers exist in the specified order; place obj_boss_base + obj_boss_phase_controller + obj_boss_weakpoint on Enemies.
3. Ensure rm_boot is the first room in the room order.
4. Camera setup:
   - obj_camera handles view creation in scr_camera_init(); no manual camera asset needed.
5. Place obj_player only if you prefer manual placement; otherwise scr_room_setup() spawns it at (32,120).
6. Add a few obj_spawn_point instances in rm_stage_01 for quick test spawns.
7. In rm_stage_01, add obj_parallax_layer to Parallax layer for visual placeholder.
8. Ensure obj_hud exists (auto-created by scr_room_setup()).
9. Run the game: boot goes to rm_stage_01, player can move, jump, shoot, and throw grenades.
