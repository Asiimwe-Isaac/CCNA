Feature,                       User Mode,                                             Privileged Mode
Prompt,                        Router>,                                                 Router#
Access Level,                  Very Limited (Read-only)                               , Full Access
Can configure device?,         No,                                                      Yes (after entering config mode)
Can see running-config?,       No,                                                      Yes
Can save config?,              No,                                                      Yes
Command to enter,              Default when you log in,                                 enable
Typical password,              None or line password,                                   Enable password / Enable secret

1. Running Configuration (running-config)

What it is:
This is the current configuration that the router or switch is actively using right now.
Where it is stored:
In RAM (volatile memory).
→ If the device loses power or is restarted, the running-config is lost.

To view it : Router# show running-config

2. Startup Configuration (startup-config)

What it is:
This is the configuration that is saved and will be loaded when the router or switch boots up or restarts.
Where it is stored:
In NVRAM (Non-Volatile RAM).
→ It survives power off and reboots.

To view it : Router# show startup-config

How to Save Running-Config to Startup-Config
This is the most important command in Cisco:Router# copy running-config startup-config

What happens:

It copies everything from running-config → startup-config.
After this command, even if you restart the device, your configuration will remain.

What is service password-encryption?
It is a security command that tells the Cisco device to hide (encrypt) all passwords that appear in plain text in the configuration files.
Without this command, passwords are stored and displayed in clear text (very insecure).
With this command, passwords are encrypted so no one can easily read them.

Recommended Best Practice Configuration
Follow these steps exactly on a router or switch in Packet Tracer:

Enter Privileged and Global Configuration Mode: Router> enable
Router# configure terminal

Set a Strong Enable Secret (This is the modern, secure way):Router(config)# enable secret MyStrongPass123!

Enable Password Encryption (to hide other passwords):Router(config)# service password-encryption

Set Line Passwords (Console and Telnet/SSH):Router(config)# line console 0
Router(config-line)# password ConsolePass456
Router(config-line)# login
Router(config-line)# exit

Router(config)# line vty 0 4
Router(config-line)# password TelnetPass789
Router(config-line)# login
Router(config-line)# transport input telnet   ← (or ssh for better security)
Router(config-line)# exit

Exit and Save the Configuration:Router(config)# exit
Router# copy running-config startup-config


Important Notes from Cisco Best Practices

enable secret is much stronger than enable password.
Always use enable secret. If both are configured, the router uses the secret one.
service password-encryption only provides weak (Type 7) protection. It is better than nothing, but not very secure. It mainly prevents passwords from showing in plain text when someone views show run.
Modern Recommendation:
Use enable secret
Use service password-encryption (for legacy compatibility)

For usernames, always use secret instead of password:Router(config)# username admin secret StrongUserPass
