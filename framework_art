#####################################################################################
#
# This script handles publishing Framework builds to artifactory.
#

import os
import sys
import re
import traceback
import pprint

import helper.messages as messages
from objects.jenkins_variables import PublishVariables
from objects.artifactory_framework_variables import ArtifactoryVariables
from objects.platforms import Platform, rename_file_in_current_diretory, zip_directory

############################################
#
# environment variables
env_var = os.environ
# Print the list of user's 
# environment variables
print("Slave Environment variable:")
pprint.pprint(dict(env_var), width = 1)
################################################


# override the variables above if testing locally
try:
    if os.environ['PUBLISH_TEST'] == "True":
        print("\tlocal debug mode")
        BASE_FOLDER_RELEASE = os.environ['BASE_FOLDER_RELEASE']
        BASE_FOLDER_SNAPSHOT = os.environ['BASE_FOLDER_SNAPSHOT']
except:
    print("\tnormal mode (i.e. not local debug mode)")

# Checking Jenkins configuration
print(messages.return_info('Checking Jenkins configuration'))
try:
    PUBLISH_VARS = PublishVariables()
    print(messages.return_info('Creating an object for {}'.format(PUBLISH_VARS.platform)))
    BUILD = Platform.factory(PUBLISH_VARS.platform)
except:
    traceback.print_exc()
    sys.exit(messages.return_error(
        'There was an issue setting the variables from Jenkins'))
finally:
    messages.print_variables(PUBLISH_VARS)

# Creating ArtifactoryVariables object
print(messages.return_info('Creating ArtifactoryVariables object'))
try:
    ART_VARS = ArtifactoryVariables(PUBLISH_VARS)
except:
    traceback.print_exc()
    sys.exit(messages.return_error('There was an issue initializing Artifactory'))
finally:
    messages.print_variables(ART_VARS)

try:
    for f in os.listdir('.'):
        BUILD.REGEX = re.compile('.*aar')
        if BUILD.REGEX.search(f):
            ART_VARS.upload_builds(f, is_test_artifact=PUBLISH_VARS.is_build_for_noPLIF, verbose=True)

    print(messages.return_success(
        '{} build file(s) uploaded to artifactory :)'.format(PUBLISH_VARS.platform)))
except:
    traceback.print_exc()
    sys.exit(messages.return_error('There was an issue uploading to artifactory'))
