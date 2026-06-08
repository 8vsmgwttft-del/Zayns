# Personal
# --- Safe environment override for port ---

if args.port is not None:

    config["listen_port"] = args.port

elif os.environ.get("DFT_PORT"):

    try:

        config["listen_port"] = int(os.environ["DFT_PORT"])

    except ValueError:

        print("Invalid DFT_PORT environment variable (must be integer)")

        sys.exit(1)

# --- Defaults (ensure required runtime keys exist) ---

config.setdefault("listen_host", "127.0.0.1")

config.setdefault("listen_port", 8080)

config.setdefault("log_level", "INFO")

# --- Log level fix ---

log_level = config.get("log_level") or "INFO"

setup_logging(log_level)

# --- Required config validation ---

required_keys = ["auth_key"]

for key in required_keys:

    if not config.get(key):

        print(f"Missing required config key: {key}")

        sys.exit(1)

# --- Mode validation ---

mode = config.get("mode", "domain_fronting")

if mode == "custom_domain":

    if not config.get("custom_domain"):

        print("Mode 'custom_domain' requires 'custom_domain'")

        sys.exit(1)

elif mode == "domain_fronting":

    for key in ("front_domain", "worker_host"):

        if not config.get(key):

            print(f"Mode 'domain_fronting' requires '{key}'")

            sys.exit(1)

elif mode == "google_fronting":

    if not config.get("worker_host"):

        print("Mode 'google_fronting' requires 'worker_host'")

        sys.exit(1)

elif mode == "apps_script":

    sid = config.get("script_ids") or config.get("script_id")

    if not sid or sid == "YOUR_APPS_SCRIPT_DEPLOYMENT_ID":

        print("Mode 'apps_script' requires valid 'script_id'")

        sys.exit(1)