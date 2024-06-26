###########################################################################
#
# Copyright 2019 Samsung Electronics All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
# either express or implied. See the License for the specific
# language governing permissions and limitations under the License.
#
###########################################################################
############################################################################
#
#   Copyright (C) 2012, 2014 Gregory Nutt. All rights reserved.
#   Author: Gregory Nutt <gnutt@nuttx.org>
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#
# 1. Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
# 2. Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in
#    the documentation and/or other materials provided with the
#    distribution.
# 3. Neither the name NuttX nor the names of its contributors may be
#    used to endorse or promote products derived from this software
#    without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
# FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
# COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
# INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
# BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS
# OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED
# AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT
# LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN
# ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.
#
############################################################################

-include $(TOPDIR)/.config
-include $(TOPDIR)/Make.defs

BUILDIRS   := $(dir $(wildcard */Make.defs))
OUTPUT_BIN_DIR = $(TOPDIR)/../build/output/bin
CONFIGURED =

define ADD_APPS
	include $(1)Make.defs
endef
$(foreach BDIR, $(BUILDIRS), $(eval $(call ADD_APPS,$(BDIR))))

define DIR_template
$(1)_$(2):
	$(Q) $(MAKE) -C $(1) $(2) TOPDIR="$(TOPDIR)" LOADABLEDIR="${LOADABLEDIR}" USER_BIN_DIR="$(OUTPUT_BIN_DIR)" CROSSDEV=$(CROSSDEV)
endef
$(foreach BDIR, $(CONFIGURED), $(eval $(call DIR_template,$(BDIR),context)))
$(foreach BDIR, $(CONFIGURED), $(eval $(call DIR_template,$(BDIR),all)))
ifeq ($(CONFIG_XIP_ELF),y)
$(foreach BDIR, $(CONFIGURED), $(eval $(call DIR_template,$(BDIR),undefsym)))
endif
$(foreach BDIR, $(BUILDIRS), $(eval $(call DIR_template,$(BDIR),preconfig)))
$(foreach CDIR, $(BUILDIRS), $(eval $(call DIR_template,$(CDIR),clean)))
$(foreach CDIR, $(BUILDIRS), $(eval $(call DIR_template,$(CDIR),distclean)))


ifeq ($(CONFIG_APP_BINARY_SEPARATION),y)
$(foreach BDIR, $(CONFIGURED), $(eval $(call DIR_template,$(BDIR),install)))

all: build install
.PHONY: all build clean install distclean depend preconfig

install: $(foreach BDIR, $(CONFIGURED), $(BDIR)_install)

else # !CONFIG_APP_BINARY_SEPARATION

all: build
.PHONY: all build clean distclean depend preconfig
endif # CONFIG_APP_BINARY_SEPARATION

build: $(foreach BDIR, $(CONFIGURED), $(BDIR)_all)

ifeq ($(CONFIG_XIP_ELF),y)
undefsym: $(foreach BDIR, $(CONFIGURED), $(BDIR)_undefsym)
endif

context: $(foreach BDIR, $(CONFIGURED), $(BDIR)_context)

preconfig: $(foreach BDIR, $(BUILDIRS), $(BDIR)_preconfig)
	$(Q) $(LOADABLEDIR)/mkkconfig.sh

clean: $(foreach CDIR, $(BUILDIRS), $(CDIR)_clean)

distclean: $(foreach CDIR, $(BUILDIRS), $(CDIR)_distclean)

-include Make.dep
