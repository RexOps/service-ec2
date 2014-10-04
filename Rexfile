use Rex -feature => ['0.54'];
use IO::All;
use Rex::Commands::Cloud;
use Rex::Commands::JobControl;
use Rex::Commands::SimpleCheck;

desc "Setup EC2";
task "setup", make {

  # read the keys from files
  # these files need to be placed in /etc/rex/aws directory.
  if ( -f "/etc/rex/aws/access.key" && -f "/etc/rex/aws/secret_access.key" ) {
    my ($access_key)        = io("/etc/rex/aws/access.key")->all;
    my ($secret_access_key) = io("/etc/rex/aws/secret_access.key")->all;

    cloud_service "Amazon";
    cloud_auth "$access_key", "$secret_access_key";
    cloud_region "ec2.eu-west-1.amazonaws.com";
  }
  else {
    Rex::Logger::info("Error: You have to place your Amazon access and secret key in the following files:", "error");
    Rex::Logger::info("  - /etc/rex/aws/access.key");
    Rex::Logger::info("  - /etc/rex/aws/secret_access.key");

    Rex::Logger::info("And you have to copy your private and public ssh key to:", "error");
    Rex::Logger::info("  - private key: /etc/rex/aws/keys/{{keyname}}.pem");
    Rex::Logger::info("  - public key : /etc/rex/aws/keys/{{keyname}}.pub");

    die("No cloud configuration found.");
  }

  my $cmdb       = get cmdb "ec2";    # get cmdb values from JobControl formular
  my $ec2_name   = $cmdb->{name};
  my $ec2_key    = $cmdb->{key};
  my $ec2_ami_id = "ami-ac8526db";

  my $instance = cloud_instance create => {
    image_id => $ec2_ami_id,
    name     => $ec2_name,
    key      => $ec2_key,
  };

  jobcontrol_add_server
    name => $instance->{ip},
    auth => {
    auth_type   => "key",
    user        => "ec2-user",
    private_key => "/etc/rex/aws/keys/$ec2_key.pem",
    public_key  => "/etc/rex/aws/keys/$ec2_key.pub",
    sudo        => TRUE,
    };

  jobcontrol_next_server $instance->{ip};

  # wait until ssh is available
  while ( !is_port_open( $instance->{ip}, 22 ) ) {
    sleep 2;
  }

  sleep 30;
};

sub generate_vm_name {
  return get_random( 8, 'a' .. 'z' );
}

1;
