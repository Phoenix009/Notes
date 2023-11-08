*How do you contact a mobile node?*
- Mobile node moves from network to network
- Correspondents want to send packets to mobile node

Two approaches:
1. **Indirect routing**:
	Communication from correspondent to mobile node goes through home agent
2. **Direct routing**:
	Correspondent gets foreign address of mobile, sends directly to mobile

- **Home network**: Permanent home of mobile
- **Permanent address**: address in home network, can always be used to reach mobile
- **Home agent**: entity that will perform mobility functions on behalf of mobile, when mobile is remote
- **Visited Network:** Network in which mobile currently resides
- **Care of address:** Address in visited network
- **Foreign agent:** Entity in visited network that performs mobility functions on behalf of mobile

**Registration:**
- mobile contacts foreign agent on entering visited network
- foreign agent contacts home agent
- Foreign agent knows about mobile
- Home agent knows about mobile

**Mobility using Indirect routing:**
- Correspondent contacts the permanent address
- Home agent intercepts the packets and forwards them to foreign agent

