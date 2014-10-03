use Rex -feature => ['0.54'];
use IO::All;
use Rex::Commands::Cloud;
use Rex::Commands::JobControl;
use Rex::Commands::SimpleCheck;

# read the keys from files
# these files need to be placed in /etc/rex/aws directory.
my ($access_key)        = io("/etc/rex/aws/access.key")->all;
my ($secret_access_key) = io("/etc/rex/aws/secret_access.key")->all;

cloud_service "Amazon";
cloud_auth "$access_key", "$secret_access_key";
cloud_region "ec2.eu-west-1.amazonaws.com";

desc "Setup EC2";
task "setup", make {
  my $cmdb       = get cmdb "ec2";    # get cmdb values from JobControl formular
  my $ec2_name   = $cmdb->{name};
  my $ec2_key    = $cmdb->{key};
  my $ec2_ami_id = "ami-1c2e8b6b";

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
};

sub generate_vm_name {
  return get_random( 8, 'a' .. 'z' );
}

1;
